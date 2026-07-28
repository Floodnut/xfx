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
<summary>Kubernetes (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Karpenter (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Cilium (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>
