# 오픈소스/클라우드 네이티브 아키텍처 변경 분석

CVE가 아니라, 정착된 오픈소스/클라우드 네이티브 프로젝트의 **실제 아키텍처 결정/재설계**를
같은 깊이(사전 지식 → 변경 분석 → 설계 결정과 트레이드오프)로 분석한다. 발견은 릴리스
노트/공식 블로그를 신호로 쓰고, 분석은 항상 실제 커밋/PR로 검증한다 — 자세한 기준은
`glossary`와 같은 스타일로 유지되는 별도 루틴 문서를 따른다.

<details>
<summary>Linux Kernel (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Live Update Orchestrator / KHO](changes/linux-kernel/6.19-live-update-orchestrator-kexec-hypervisor.md) | kexec로 VM을 안 끄고 커널을 업데이트하는 프레임워크 — KHO의 radix tree/FDT 기반 메모리 보존과 LUO의 콜백 기반 자원 생명주기 관리 |

</details>

<details>
<summary>Kubernetes (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [In-Place Pod Resize GA (KEP-1287)](changes/kubernetes/1.35-in-place-pod-resize-ga.md) | Pod 재시작 없이 CPU/메모리를 바꾸는 기능이 v1.35에서 GA — Desired/Allocated/Actuated/Actual 4단계 상태 기계 |

</details>

<details>
<summary>Karpenter (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Disruption Budgets (NodePool)](changes/karpenter/1.0-disruption-budgets-nodepool.md) | v1.0에서 NodePool.Spec.Disruption.Budgets 도입 — cron 시간창 × 동시성 제한 교집합 방식으로 노드 제거 통제 |

</details>

<details>
<summary>Cilium (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [로드밸런싱 컨트롤 플레인 재설계](changes/cilium/1.18-loadbalancer-statedb-redesign.md) | v1.18에서 뮤텍스+해시맵 기반 명령형 모델을 StateDB 테이블 기반 데이터 중심 모델로 전환 |

</details>
