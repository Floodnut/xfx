# Double Free (이중 해제)

**정의**: 이미 `free()`로 반납한 메모리를 다시 한 번 `free()`하는 버그다.

**왜 문제가 되는가**: 메모리 할당자(allocator)는 내부적으로 "해제된 블록" 목록을 관리하는데, 같은 블록을 두 번 반납하면 이 목록이 오염된다. 공격자가 그 사이에 같은 크기의 다른 할당을 끼워넣으면, 할당자의 내부 메타데이터를 조작해 임의 주소에 쓰기(write-what-where)로 이어질 수 있다.

**간단한 예시**:
```c
free(ptr);
// ... 에러 처리 경로 등에서 실수로 ...
free(ptr);   // 같은 메모리를 또 반납 — allocator 내부 상태 오염
```

**이 저장소의 예**: [CVE-2006-5051](../vulnerability/linux/CVE-2006-5051-openssh-sigalrm-cleanup-double-free.md) (OpenSSH SIGALRM 핸들러)
