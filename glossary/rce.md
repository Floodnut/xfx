# RCE (Remote Code Execution, 원격 코드 실행)

**정의**: 공격자가 네트워크를 통해(로컬 접근 없이) 대상 시스템에서 자신이 원하는 코드를 실행시킬 수 있게 되는 것이다. 취약점의 "원인"이 아니라 그 취약점이 도달할 수 있는 결과(임팩트) 중 가장 심각한 축에 속한다.

**왜 이 용어가 자주 나오는가**: 리포트의 개요에서 "이 취약점은 RCE로 이어질 수 있다"처럼, 근본 원인(메모리 손상, 역직렬화, 명령어 인젝션 등)과 별개로 "결국 뭘 할 수 있게 되는가"를 한마디로 요약할 때 쓴다. 같은 RCE라도 도달 경로는 완전히 다를 수 있다 — [역직렬화](deserialization.md)로 갈 수도, [명령어 인젝션](command-injection.md)이나 [메모리 손상](memory-corruption.md)을 통해 갈 수도 있다.

**이 저장소의 예**: [CVE-2026-0770](../vulnerability/opensource/CVE-2026-0770-langflow-validate-code-exec-rce.md) (Langflow, `exec()` 직접 호출을 통한 RCE)
