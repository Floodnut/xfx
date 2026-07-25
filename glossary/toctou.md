# TOCTOU (Time-Of-Check to Time-Of-Use)

**정의**: "검사 시점(Time-Of-Check)"과 "실제 사용 시점(Time-Of-Use)" 사이에 대상이 바뀔 수 있어서, 검사했던 대상과 실제로 쓰는 대상이 달라지는 [레이스 컨디션](race-condition.md)의 한 종류다. 파일 시스템 관련 취약점에서 특히 자주 나온다.

**왜 문제가 되는가**: "이 경로가 안전한지 확인했다"는 사실이, 확인한 그 순간에만 유효하다. 확인과 사용 사이에 그 경로가 심볼릭 링크로 바뀌거나 다른 파일로 대체되면, 검사는 통과했지만 실제로 열리는 건 전혀 다른 파일이다.

**간단한 예시**:
```c
if (access("/tmp/file", W_OK) == 0) {   // 검사(Check): 쓰기 가능한지 확인
    // 이 사이에 공격자가 /tmp/file 을 심볼릭 링크로 교체
    fd = open("/tmp/file", O_WRONLY);   // 사용(Use): 확인했던 그 파일이 아닐 수 있음
}
```

**이 저장소의 예**: [CVE-2026-35355](../vulnerability/opensource/CVE-2026-35355-uutils-install-toctou-symlink.md) (uutils install)
