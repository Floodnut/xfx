# 2026-08-24 TCMalloc 샘플 할당의 resident telemetry 분리

## 메타데이터

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | TCMalloc, `google/tcmalloc` |
| 적용 날짜 | 2026-08-24 UTC, 기본 활성화 커밋 기준 |
| 도입 PR 및 커밋 | [PR #545](https://github.com/google/tcmalloc/pull/545), [커밋 `f78ae869`](https://github.com/google/tcmalloc/commit/f78ae869163eec33792c2308a3195bc824dfdeb1), 2026-08-23 UTC |
| 기본 활성화 | [PR #560](https://github.com/google/tcmalloc/pull/560), [커밋 `56256560`](https://github.com/google/tcmalloc/commit/56256560e32efe081ab20aeadf95c4768919ef89), 2026-08-24 UTC |
| 발견 출처 | `master`의 [공식 커밋 이력](https://github.com/google/tcmalloc/commits/master)과 PR #545, #560 |
| 검증 근거 | [`SampleifyAllocation()`의 적용 경로](https://github.com/google/tcmalloc/blob/f78ae869163eec33792c2308a3195bc824dfdeb1/tcmalloc/allocation_sampling.h), [residency 회귀 테스트](https://github.com/google/tcmalloc/blob/f78ae869163eec33792c2308a3195bc824dfdeb1/tcmalloc/testing/heap_profiling_test.cc), [`SystemAllocator::Release()`](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/tcmalloc/internal/system_allocator.h), [holdback 설정](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/tcmalloc/experiment_config.h) |
| 소스 라이선스 | [Apache License 2.0](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/LICENSE), 본문은 코드 재게시 없이 동작을 요약 |

## 개요

TCMalloc은 heap profile의 resident, stale, zero telemetry가 현재 샘플 할당의 실제 접근 상태를 나타내도록, 새 sampled 또는 cold allocation의 첫 하드웨어 페이지 뒤 범위를 `SystemAllocator::Release()`로 처리하는 정책을 추가했다. 같은 가상 주소가 재사용되면 이전 객체가 남긴 resident page가 새 객체의 profile에 섞일 수 있었기 때문이다. 변경은 먼저 기본 비활성 선택지로 도입됐고, 다음 커밋에서 기본 활성화하되 holdback experiment로 되돌릴 수 있게 했다.

## 사전 지식

### TCMalloc sampling과 resident telemetry

TCMalloc은 일반 `malloc`/`new` 요청의 일부를 샘플로 잡아 호출 스택, 할당 크기, 접근 정보를 heap profile에 기록한다. 공식 [설계 문서](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/docs/design.md)는 low-overhead sampling을 애플리케이션 메모리 사용 관측 기능으로 설명한다. 이 관측은 모든 할당을 계측하지 않고 샘플의 정보를 확대해 사용하므로, 선택된 샘플의 page 상태가 정확해야 profile의 해석도 정확하다.

resident telemetry는 해당 할당 범위의 page가 물리 메모리에 남아 있는지를 관측한다. 그러나 `free`는 가상 주소의 과거 사용 이력을 지우는 사건이 아니다. allocator가 같은 범위를 다음 객체에 다시 배정할 수 있고, 앞 객체가 touch한 page는 다음 객체가 아직 쓰지 않아도 resident로 남을 수 있다.

도입 커밋은 256 KiB를 전부 touch한 뒤 free하고, 같은 256 KiB를 다시 받아 앞 128 KiB만 touch하는 사례를 제시한다. 변경 전에는 새 할당의 상위 128 KiB까지 resident로 관측될 수 있었다. 이 값은 현재 live allocation이 사용한 메모리라기보다 동일 주소에 있던 이전 allocation의 흔적을 포함한다.

### sampling 경로의 보호 대상

`SampleifyAllocation()`은 샘플 할당을 profile recorder에 등록하기 전 span과 allocation 상태를 가진다. 이 경로에는 일반 샘플 외에도 cold allocation과 GWP-ASan guarded allocation이 들어올 수 있다. guarded allocation의 page에는 deallocation 시 buffer overflow를 감지할 canary가 있으므로, 도입 커밋은 해당 할당을 release 대상에서 명시적으로 제외한다.

`SystemAllocator::Release()`는 OS에 page를 회수하거나 zero할 수 있음을 알리는 인터페이스다. 이후 프로그램이 그 범위를 다시 touch하면 page fault가 발생할 수 있다. 따라서 이는 단순한 profile 표시 변경이 아니라, 관측 정확성과 실제 allocation 경로의 page 상태를 함께 바꾸는 정책이다.

## 변경 분석

### 변경 전

변경 전 sampling 경로는 새 샘플의 span을 profile에 등록했지만, 주소 재사용으로 이미 resident인 page를 새 allocation의 이력과 분리하지 않았다. allocation이 free된 뒤에도 같은 주소의 page가 resident라면, 새 객체가 아직 touch하지 않은 영역까지 profile의 resident, stale, zero 판단에 영향을 줄 수 있었다.

```text
[변경 전] 주소 재사용이 profile에 남기는 이력

이전 sampled allocation
  256 KiB 전체 touch
          |
          v
        free
          |
          v
같은 주소에 새 sampled allocation
  앞 128 KiB만 touch
          |
          v
나머지 128 KiB의 과거 resident page 유지
          |
          v
profile이 현재 객체보다 큰 resident 범위를 관측할 수 있음
```

도입 커밋 `f78ae869`은 이 문제를 독립 parameter `tcmalloc_madvise_sampled_allocations`로 감쌌다. 기본값은 disabled이며, `SampleifyAllocation()`은 parameter가 enabled이고 allocation이 guarded가 아닐 때만 memory tag를 확인한다. `kSampled`, `kSampledP1`, `kCold` tag에는 첫 하드웨어 page를 남기고 나머지 span에 `Release()`를 호출한다. `kNormal`, `kNormalP1`, `kMetadata`는 이 정책의 대상이 아니다.

회귀 테스트는 재할당 뒤 첫 page만 touch하는 흐름을 만들어, enabled 상태의 sampled 및 cold allocation에서 resident byte가 touch한 범위에 맞게 바뀌는지 확인한다. 반대로 normal allocation과 guarded allocation이 정책에서 제외되는 조합도 검사한다. 테스트의 핵심은 RSS 절감량이 아니라, profile이 주소의 과거 사용보다 현재 live allocation의 접근을 반영하는지다.

## 설계 결정 및 트레이드오프

### 변경 후

기본 활성화 커밋 `56256560`은 parameter의 초기값을 enabled로 바꾸고, `TCMALLOC_SONIC_MADVISE_SAMPLED_ALLOCATIONS_HOLDBACK` experiment가 활성화된 경우에만 disabled로 되돌린다. experiment 설정의 rollout 범위는 0부터 0.5이며, 별도 test variant가 holdback 상태도 검증한다. 즉 기본 정책은 정확한 profile이지만, 배포 범위 일부에서는 이전 동작을 유지할 수 있다.

```text
[변경 후] 선택적 release와 holdback

새 allocation이 sample로 선택됨
          |
          +-- guarded ----------> release하지 않음, canary 보존
          |
          +-- normal/metadata --> release하지 않음, 정책 범위 밖
          |
          +-- sampled/cold ----> 첫 하드웨어 page 보존
                                  |
                                  v
                            나머지 span Release()
                                  |
                                  v
                       현재 allocation이 touch한 page를 다시 관측

기본값 enabled
  holdback experiment 활성화 시 disabled
```

대안과 트레이드오프는 다음과 같다.

- 모든 allocation에 release 적용: 구현 범위는 넓지만 normal, metadata, guarded allocation의 의미와 안전성 훼손
- 기본 비활성 상태 유지: 기본 profile의 정확성 개선 미적용
- sampled/cold 비보호 allocation만 선택적으로 release하고 holdback 제공: 적용 범위를 제한하면서 기본 정확성과 운영 복귀 경로 확보

선택한 방식도 비용을 없애지는 않는다. release된 page를 나중에 application이 touch하면 다시 fault-in될 수 있다. 또한 첫 하드웨어 page를 제외하는 이유는 코드에 재검토 TODO만 남아 있어 성능 효과로 단정할 수 없다. TCMalloc은 이 불확실성을 모든 allocation에 퍼뜨리는 대신, sample 관련 tag와 guarded 예외로 경계를 두고 기본 활성화 뒤 holdback을 준비했다.

## 더 살펴볼 점

- holdback experiment의 실제 운영 지표와 종료 기준
- 첫 하드웨어 page를 보존하는 정책의 향후 변경 여부
- hugepage 환경에서 release가 heap profile 정확성과 allocation latency에 주는 영향

## 참고 자료

- [PR #545](https://github.com/google/tcmalloc/pull/545), 주소 재사용과 telemetry 왜곡 문제, 기본 비활성 도입
- [도입 커밋 `f78ae869`](https://github.com/google/tcmalloc/commit/f78ae869163eec33792c2308a3195bc824dfdeb1), sampling 경로, parameter, 회귀 테스트
- [도입 전 부모 커밋 `13c53eab`](https://github.com/google/tcmalloc/commit/13c53eab718fe55620672f980631683a53cfd186), release 호출이 없던 sampling 경로
- [PR #560](https://github.com/google/tcmalloc/pull/560), 기본 활성화와 profile 정확성 목표
- [기본 활성화 커밋 `56256560`](https://github.com/google/tcmalloc/commit/56256560e32efe081ab20aeadf95c4768919ef89), holdback experiment, parameter 초기화, test variant
- [TCMalloc 설계 문서 at `56256560`](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/docs/design.md), sampling과 allocator 구조의 배경
- [TCMalloc Apache-2.0 license at `56256560`](https://github.com/google/tcmalloc/blob/56256560e32efe081ab20aeadf95c4768919ef89/LICENSE), 코드 참고 조건

### 참고 외 별도 확인 링크

*본문 참고 자료가 실제 코드, PR, 회귀 테스트와 Apache-2.0 라이선스 확인으로 충분하다.*
