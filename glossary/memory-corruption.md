# Memory Corruption (메모리 손상)

**정의**: 프로그램이 의도하지 않은 방식으로 자신의 메모리 내용을 변경해버리는 상태를 통칭하는 넓은 개념이다. [버퍼 오버플로우](buffer-overflow.md), [Use-After-Free](use-after-free.md), [이중 해제](double-free.md), [타입 컨퓨전](type-confusion.md) 등이 모두 메모리 손상을 일으키는 구체적인 버그 유형들이다.

**왜 이 용어가 따로 필요한가**: 리포트에서 "이 버그는 메모리 손상으로 이어진다"처럼, 구체적인 유형을 특정하기 전에 "메모리 상태가 프로그램의 가정과 달라졌다"는 결과 자체를 가리킬 때 쓴다. 원인(왜 손상됐는가)과 결과(메모리가 손상된 상태에서 무엇을 할 수 있는가)를 구분해서 설명할 때 유용한 상위 개념이다.

**관련 개념**: [버퍼 오버플로우](buffer-overflow.md), [Use-After-Free](use-after-free.md), [이중 해제](double-free.md), [범위 밖 읽기/쓰기](out-of-bounds.md), [타입 컨퓨전](type-confusion.md)

**이 저장소의 예**: [CVE-2025-21333](../vulnerability/windows/CVE-2025-21333-windows-hyperv-crossvmevent-heap-overflow.md) (Hyper-V 힙 오버플로우 → 커널 객체 손상)
