# Symlink Attack (심볼릭 링크 공격)

**정의**: 심볼릭 링크(바로가기 파일)를 이용해, 프로그램이 접근하려는 경로가 실제로는 공격자가 지정한 다른 파일/디렉터리를 가리키게 만드는 공격이다. [TOCTOU](toctou.md)와 자주 결합된다 — "이 경로는 안전하다"고 검사한 뒤 실제 접근하기 전에 심볼릭 링크로 바꿔치기하는 식이다.

**왜 문제가 되는가**: 권한이 높은 프로세스(setuid 바이너리 등)가 사용자가 쓸 수 있는 디렉터리 안의 경로를 다룰 때, 그 경로가 심볼릭 링크일 수 있다는 걸 검증하지 않으면 공격자가 그 권한으로 임의 파일에 접근하게 만들 수 있다.

**간단한 예시**:
```
$ ln -s /etc/shadow /tmp/myfile.txt
# 이후 상위 권한 프로세스가 /tmp/myfile.txt를 열면
# 실제로는 /etc/shadow를 여는 것과 같아짐
```

**이 저장소의 예**: [CVE-2015-5602](../vulnerability/linux/CVE-2015-5602-sudoedit-symlink-parent-dir-check-bypass.md) (sudoedit)
