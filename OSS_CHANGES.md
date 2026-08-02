# 오픈소스/클라우드 네이티브 아키텍처 변경 분석

CVE가 아니라, 정착된 오픈소스/클라우드 네이티브 프로젝트의 **실제 아키텍처 결정/재설계**를
같은 깊이(사전 지식 → 변경 분석 → 설계 결정과 트레이드오프)로 분석한다. 발견은 릴리스
노트/공식 블로그를 신호로 쓰고, 분석은 항상 실제 커밋/PR로 검증한다 — 자세한 기준은
`glossary`와 같은 스타일로 유지되는 별도 루틴 문서를 따른다.

<details>
<summary>Linux Kernel (2)</summary>

| 변경 | 내용 |
| --- | --- |
| [Live Update Orchestrator / KHO](oss-changes/linux-kernel/6.19-live-update-orchestrator-kexec-hypervisor.md) | kexec로 VM을 안 끄고 커널을 업데이트하는 프레임워크 — KHO의 radix tree/FDT 기반 메모리 보존과 LUO의 콜백 기반 자원 생명주기 관리 |
| [Linux Kernel pidfd Process Lifecycle: CLONE_AUTOREAP/CLONE_PIDFD_AUTOKILL](oss-changes/linux-kernel/7.1-pidfd-process-lifecycle-autoreap-autokill.md) | clone3()에 CLONE_AUTOREAP/CLONE_PIDFD_AUTOKILL 플래그를 추가해, 부모 전체에 걸리던 SIGCHLD 기반 auto-reap을 자식 단위로 세분화하고 pidfd 소유권에 자식 생명주기를 묶었다. |

</details>

<details>
<summary>Kubernetes (2)</summary>

| 변경 | 내용 |
| --- | --- |
| [In-Place Pod Resize GA (KEP-1287)](oss-changes/kubernetes/1.35-in-place-pod-resize-ga.md) | Pod 재시작 없이 CPU/메모리를 바꾸는 기능이 v1.35에서 GA — Desired/Allocated/Actuated/Actual 4단계 상태 기계 |
| [Server-Side Sharded List/Watch](oss-changes/kubernetes/1.36-server-side-sharded-list-and-watch.md) | shardSelector로 LIST/WATCH 필터링을 API 서버(워치 캐시)로 옮겨 컨트롤러 수평 확장 시 레플리카 수에 비례해 커지던 네트워크/CPU 낭비를 없앤 KEP-5866 Alpha 기능. |

</details>

<details>
<summary>Karpenter (4)</summary>

| 변경 | 내용 |
| --- | --- |
| [Disruption Budgets (NodePool)](oss-changes/karpenter/1.0-disruption-budgets-nodepool.md) | v1.0에서 NodePool.Spec.Disruption.Budgets 도입 — cron 시간창 × 동시성 제한 교집합 방식으로 노드 제거 통제 |
| [v1.14 — Karpenter Balanced Consolidation](oss-changes/karpenter/1.14-balanced-consolidation-scoring.md) | 저장액 대비 disruption 비율을 점수화해 손해 보는 통합(consolidation)을 걸러내는 새 consolidationPolicy: Balanced 도입 |
| [Karpenter CapacityBuffer: 가상 파드로 여유 용량을 미리 만들어두는 사전 프로비저닝](oss-changes/karpenter/1.14-capacity-buffer-active-provisioning.md) | v1.14에서 alpha로 추가된 CapacityBuffer API가 파드 없이도 노드를 미리 켜두는 방식(가상 파드를 매 루프 주입)과, 그로 인한 노미네이션/emptiness/consolidation 경계 처리를 다룬다. |
| [Karpenter Dynamic Resource Allocation Scheduling](oss-changes/karpenter/1.14-dynamic-resource-allocation-scheduling.md) | Karpenter가 아직 인스턴스 타입이 확정되지 않은 NodeClaim 상태에서 GPU 등 DRA 디바이스를 배분하기 위해 전용 할당기(pkg/scheduling/dynamicresources)를 새로 구현하고 스케줄러/디스럽션/노드 초기화 전반에 통합한 변경을 분석. |

</details>

<details>
<summary>Cilium (2)</summary>

| 변경 | 내용 |
| --- | --- |
| [로드밸런싱 컨트롤 플레인 재설계](oss-changes/cilium/1.18-loadbalancer-statedb-redesign.md) | v1.18에서 뮤텍스+해시맵 기반 명령형 모델을 StateDB 테이블 기반 데이터 중심 모델로 전환 |
| [로드밸런서 백엔드 평탄화 (Aggregated Load-Balancer State)](oss-changes/cilium/1.20-loadbalancer-backend-flatten.md) | v1.19에서 서비스별 인스턴스를 중첩 맵으로 담던 백엔드 행 구조가 실제 프로덕션 메모리 급증을 유발한 사례 — v1.20에서 (서비스,주소,우선순위) 조합마다 독립된 테이블 행으로 평탄화해 해결 |

</details>

<details>
<summary>OPA (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [컨테이너 자원 인지 (GOMAXPROCS/GOMEMLIMIT)](oss-changes/opa/1.18.0-container-aware-gomaxprocs-gomemlimit.md) | v1.18.0에서 automaxprocs 복원 + automemlimit 신규 추가 — Go 네이티브 cgroup 인지의 최소값 차이(1 vs 2)로 저메모리 배포에서 발생한 OOM 회귀를 되돌린 사례 |

</details>

<details>
<summary>LoxiLB (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [LoxiLB HA Egress Cluster Routing](oss-changes/loxilb/0.9.8-ha-egress-cluster-routing.md) | v0.9.8에서 기존 LB·VIP·HA 상태 기계를 egress 모드로 연결해, 대기 노드의 트래픽을 전용 VXLAN으로 활성 노드에 전달하고 안정적인 SNAT/VIP를 유지하는 초기 설계. |

</details>

<details>
<summary>Ceph (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Ceph mgmt-gateway High Availability](oss-changes/ceph/20.2.0-mgmt-gateway-ha.md) | Tentacle에서 Dashboard와 monitoring endpoint를 NGINX 기반 단일 TLS 경계로 모으고, virtual IP·keepalived·stateless oauth2-proxy로 gateway 자체의 HA까지 보완한 설계. |

</details>

<details>
<summary>OVN (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [OVN Flow-Based Tunnels](oss-changes/ovn/26.03-flow-based-tunnels.md) | v26.03에서 원격 chassis별 tunnel port 대신 type별 shared port를 만들고 OpenFlow가 패킷마다 tunnel endpoint를 설정해 대규모 환경의 port 수를 줄인 실험적 설계. |

</details>
