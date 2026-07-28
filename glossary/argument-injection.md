# Argument Injection (인자 인젝션)

**정의**: 애플리케이션이 외부 프로세스를 실행할 때, 사용자 입력이 명령 문자열이 아니라 이미 배열로 나뉜 인자 목록(argv) 중 하나로 들어가더라도, 그 입력값 자체가 `-`나 `--`로 시작하는 옵션/스위치 형태를 취할 수 있게 허용하면 대상 프로그램이 이를 데이터가 아니라 별도의 커맨드라인 옵션으로 해석해버리는 취약점이다.

**왜 문제가 되는가**: Command Injection과 달리 셸 메타문자나 명령 구분자가 전혀 필요 없다 — `subprocess.run([binary, "--flag", user_input])`처럼 인자 배열을 안전하게 구성해도, `user_input` 자체가 `--another-flag=value` 형태이면 대상 프로그램은 그걸 값이 아니라 새로운 옵션으로 받아들인다. 특히 실행 중인 자식 프로세스의 생성 방식이나 권한, 디버깅 훅을 바꾸는 옵션(예: 브라우저의 프로세스 실행기/샌드박스 우회 관련 스위치)이 이렇게 주입되면 원격 코드 실행으로 이어질 수 있다.

**간단한 예시**:
```python
# 안전해 보이지만 취약: 셸은 안 거치지만 인자 배열 자체를 신뢰
subprocess.run(["some-cli", "--input-file", user_supplied_value])

# user_supplied_value = "--exec-hook=/tmp/payload" 이면
# some-cli는 이걸 파일명이 아니라 새 옵션으로 해석할 수 있다.

# 완화: "--" 뒤는 옵션으로 해석하지 않는 관례를 쓰거나, 입력값이
# "-"/"--"로 시작하지 못하게 애플리케이션 레벨에서 명시적으로 막는다.
```

**이 저장소의 예**: [CVE-2026-57572](../vulnerability/browser/CVE-2026-57572-crawl4ai-chromium-argument-injection.md) (Crawl4AI) — 사용자가 통제하는 값이 Chromium 실행 인자 목록에 그대로 섞여 들어가, `--utility-cmd-prefix`/`--renderer-cmd-prefix`/`--no-zygote` 같은 자식 프로세스 실행 방식을 바꾸는 스위치로 주입돼 원격 코드 실행에 이른 사례.
