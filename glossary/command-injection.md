# Command Injection (명령어 인젝션)

**정의**: 애플리케이션이 사용자 입력을 셸 명령이나 다른 프로세스를 실행하는 문자열에 그대로 섞어 넣을 때, 그 입력에 셸 메타문자(`;`, `|`, `` ` ``, `$()` 등)를 넣어 원래 의도한 것과 다른 명령을 실행시키는 취약점이다.

**왜 문제가 되는가**: 셸은 저 메타문자들을 "명령 구분자"나 "명령 치환"으로 해석하기 때문에, 사용자 입력이 데이터가 아니라 명령의 일부로 취급되면 임의 명령 실행으로 직결된다.

**간단한 예시**:
```python
# 취약한 코드
os.system(f"ping -c 1 {user_input}")

# user_input = "8.8.8.8; rm -rf /important" 이면
# 실제로 실행되는 건: ping -c 1 8.8.8.8; rm -rf /important
```

**이 저장소의 예**: [CVE-2026-62392](../vulnerability/opensource/CVE-2026-62392-apache-kylin-async-query-os-command-injection.md) (Apache Kylin)
