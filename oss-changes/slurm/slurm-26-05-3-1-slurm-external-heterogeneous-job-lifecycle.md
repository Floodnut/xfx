# slurm-26-05-3-1 Slurm 외부 이종 작업 수명주기

## 메타데이터

| 항목 | 내용 |
| --- | --- |
| 프로젝트 | Slurm |
| 적용 릴리스 | slurm-26-05-3-1, 2026-08-13 공개 |
| 핵심 변경 | [`a22d6bf`](https://github.com/SchedMD/slurm/commit/a22d6bf73ead8aa13c5cd08a8a95216b67b036f4), [`bafcbe5`](https://github.com/SchedMD/slurm/commit/bafcbe5cda32591c7cacf9dc90668d31e3a43cf4), [`dbf2614`](https://github.com/SchedMD/slurm/commit/dbf2614351194e00af27591298c89ac18ccb523e), [`baf87a0`](https://github.com/SchedMD/slurm/commit/baf87a049587bdb23fdcab47e3190c353d320758), [`170c907`](https://github.com/SchedMD/slurm/commit/170c907bd2b4b98f2198d6a9610bd357c9059139) |
| 발견 출처 | [Slurm 26.05.3 릴리스 노트](https://github.com/SchedMD/slurm/releases/tag/slurm-26-05-3-1) |
| 검증 근거 | 위 커밋들의 `proc_req.c`, `job_scheduler.c`, `sbatch/opt.c` 및 `sbatch/sbatch.c` diff |

## 개요

Slurm 26.05.3은 외부 컴포넌트를 포함한 heterogeneous job을 정식 수명주기에 넣었다. 이종 작업의 각 컴포넌트가 모두 배치 스크립트를 가져야 한다는 기존 가정을 버리고, 일반 컴포넌트와 스크립트가 없는 외부 컴포넌트를 조합할 때 리더가 스크립트와 최종 기동을 책임지도록 재구성했다. 그 결과 `sbatch`와 REST 제출, 스케줄링, 기동이 같은 작업 분류 규칙을 따르게 됐다.

## 사전 지식

### heterogeneous job의 리더와 배치 스크립트

Slurm heterogeneous job은 자원 요구가 다른 여러 컴포넌트를 하나의 작업으로 묶는다. 컨트롤러는 이들을 개별 job record로 관리하지만, 리더 컴포넌트와 같은 이종 작업 식별자로 묶어 제출과 기동을 조정한다.

기존 batch heterogeneous job 경로는 각 컴포넌트가 배치 스크립트를 갖는다는 가정으로 구성됐다. 컨트롤러는 모든 컴포넌트에 `batch_flag`를 설정하고, 배치 스크립트의 실행 진입점인 `launch_job()`이 리더를 기동하는 방식에 기대고 있었다.

외부 컴포넌트는 배치 스크립트가 없다. 따라서 외부 컴포넌트를 이종 작업에 섞으려면 단순히 제출을 허용하는 것만으로는 부족하다. 스크립트가 없는 컴포넌트가 준비된 뒤에도, 스크립트를 가진 리더를 어느 지점에서 기동할지와 작업 전체의 스크립트 소유권을 정의해야 한다.

## 변경 분석

### 모든 컴포넌트가 스크립트를 가진다는 가정

변경 전에는 컨트롤러가 이종 작업의 각 job record에 `batch_flag = 1`을 설정했다. 외부 컴포넌트에는 스크립트가 없는데도 batch job으로 취급되므로, 외부 이종 작업은 제출과 실행 경로에서 충돌했다. 커밋 `dbf2614`는 이 가정 때문에 컨트롤러 재시작 뒤 리더 job이 삭제되거나 실행 시 `fatal()`이 발생할 수 있었다고 기록한다.

```text
변경 전

[sbatch heterogeneous job]
     |
     v
[leader] [component 1] [component 2]
     |          |             |
     +----------+-------------+
                |
                v
      [모든 컴포넌트에 batch_flag]
                |
                v
      [모든 컴포넌트가 스크립트를 가질 것이라는 가정]
                |
                v
      [외부 컴포넌트의 script-less 상태와 충돌]
```

이 구조에서는 다음 책임이 분명하지 않았다.

- 혼합 작업에서 배치 스크립트를 보유할 단일 컴포넌트
- 스크립트 없는 외부 컴포넌트가 준비된 뒤 리더 기동을 재시도할 주체
- 제출 옵션 `--external`을 각 컴포넌트에 독립 적용하는 방식

## 설계 결정 및 트레이드오프

### 리더의 스크립트 소유권과 준비 완료 신호 분리

커밋 `a22d6bf`는 이종 작업을 세 종류로 정의한다. regular은 모든 컴포넌트가 일반 `slurmd` 컴포넌트인 경우, external은 모두 외부인 경우, mixed는 일반 리더와 외부 또는 일반 추가 컴포넌트를 함께 둔 경우다. 리더가 external이면 모든 컴포넌트도 external이어야 하며, mixed 작업에서는 일반 리더가 배치 스크립트를 보유한다.

`dbf2614`는 external 컴포넌트에만 `batch_flag`를 설정하지 않는다. 이어서 `bafcbe5`는 script-less 컴포넌트가 준비될 때 `launch_het_job_leader()`를 호출한다. 이 helper는 리더를 찾아, 리더가 batch job이고 아직 configuring 상태가 아닐 때만 기존 `launch_job()`을 호출한다. 외부 컴포넌트가 직접 스크립트를 실행하지 않고도 마지막 준비 신호가 리더 기동으로 이어지는 구조다.

```text
변경 후

[sbatch heterogeneous job]
     |
     v
[분류]
  | regular: 모두 일반 컴포넌트
  | external: 모두 외부 컴포넌트
  + mixed: 일반 리더 + 외부 또는 일반 추가 컴포넌트
     |
     v
[mixed leader: batch script 보유, batch_flag]
     |
     +-- [외부 component: script-less, batch_flag 없음]
                     |
                     v
            [준비 시 launch_het_job_leader]
                     |
                     v
              [리더 상태 확인 후 launch_job]
```

제출 경로도 이 불변식을 따른다. `170c907`은 `--external`을 각 이종 컴포넌트마다 다시 해석하게 하고, 첫 컴포넌트가 external일 때만 스크립트와 `--wrap`을 거부한다. `baf87a0`은 burst-buffer 플러그인이 외부 컴포넌트의 존재하지 않는 스크립트를 조작하려 하지 않도록 하며, mixed 작업의 스크립트는 리더에서만 가져온다.

선택한 방식의 장단점은 다음과 같다.

- 각 컴포넌트에 스크립트를 강제: 기존 기동 모델은 단순하지만 external job을 표현할 수 없는 제약
- external과 regular 조합을 전면 금지: 실행 경로 변경은 적지만 동일한 작업 안의 자원 모델을 확장하지 못하는 제약
- 리더만 스크립트와 최종 기동을 책임: 작업 분류와 수명주기 불변식이 명확해지는 대신, 외부 컴포넌트의 준비 이벤트마다 리더 상태를 다시 확인하는 조정 비용

일반 heterogeneous job의 동작은 이 helper를 사용하지 않는다. 대신 external 리더 뒤에 일반 컴포넌트를 두는 순서는 계속 거부한다. 스크립트가 없는 리더와 스크립트를 요구하는 후속 컴포넌트를 섞지 않음으로써, 어느 컴포넌트가 작업 전체의 batch launch를 책임지는지 하나로 고정한다.

## 더 살펴볼 점

- external mixed job의 취소, 재시작, controller failover에서 리더 launch 재시도 순서
- REST 제출 경로와 `sbatch` 경로가 같은 컴포넌트 분류 오류를 반환하는지의 회귀 테스트
- external 컴포넌트에 연결되는 burst-buffer, accounting, reservation 정책의 후속 확장

## 참고 자료

### 참고 외 별도 확인 링크

*Slurm 저장소의 GitHub 라이선스 메타데이터는 `NOASSERTION`으로 반환돼, 별도 재사용 라이선스를 확인하지 못했다. 아래 자료는 정확한 변경 추적용으로만 사용했으며 본문에 원문 코드나 설명을 재게시하지 않았다.*

- [Slurm 26.05.3 릴리스 노트](https://github.com/SchedMD/slurm/releases/tag/slurm-26-05-3-1), 발견 출처
- [작업 분류와 리더 제약 커밋 a22d6bf](https://github.com/SchedMD/slurm/commit/a22d6bf73ead8aa13c5cd08a8a95216b67b036f4)
- [외부 컴포넌트의 batch flag 변경 dbf2614](https://github.com/SchedMD/slurm/commit/dbf2614351194e00af27591298c89ac18ccb523e)
- [script-less 컴포넌트의 리더 기동 helper bafcbe5](https://github.com/SchedMD/slurm/commit/bafcbe5cda32591c7cacf9dc90668d31e3a43cf4)
- [제출 경로 변경 baf87a0, 170c907](https://github.com/SchedMD/slurm/commit/170c907bd2b4b98f2198d6a9610bd357c9059139)
