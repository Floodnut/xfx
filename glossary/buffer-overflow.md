# Buffer Overflow (버퍼 오버플로우) — Heap/Stack Overflow

**정의**: 정해진 크기의 메모리 버퍼에, 그 크기를 넘는 데이터를 써서 버퍼 바로 뒤(또는 앞)의 메모리를 덮어써버리는 버그다. 그 버퍼가 스택에 있으면 스택 오버플로우, 힙에 있으면 힙 오버플로우라고 부른다.

**왜 문제가 되는가**: 버퍼 뒤에는 보통 다른 변수, 함수의 리턴 주소, 객체의 다른 필드, 또는 힙 할당자의 메타데이터가 있다. 이걸 덮어쓰면 실행 흐름을 바꾸거나(리턴 주소 조작) 다른 객체의 상태를 조작할 수 있다.

**간단한 예시**:
```c
char buf[16];
strcpy(buf, user_input);   // user_input이 16바이트보다 길면 buf 뒤 메모리까지 덮어씀
```

**이 저장소의 예**: [CVE-2018-12326](../vulnerability/opensource/CVE-2018-12326-redis-cli-buffer-overflow.md) (redis-cli 스택 오버플로우), [CVE-2026-56645](../vulnerability/browser/CVE-2026-56645-edge-heap-buffer-overflow.md) (Edge 힙 오버플로우)
