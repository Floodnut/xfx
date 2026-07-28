# 용어집

이 저장소의 취약점 분석 리포트에서 반복적으로 등장하는 **소프트웨어 무관 범용 개념**들을 정리한다. 각 리포트의 "사전 지식" 섹션은 그 소프트웨어 고유의 동작 방식을 설명하는 데 집중하고, 여기 실린 범용 개념은 최초 등장 시 링크만 걸어서 참조한다.

## 메모리 손상 계열

- [Memory Corruption (메모리 손상)](glossary/memory-corruption.md) — 상위 개념
- [Use-After-Free (UAF)](glossary/use-after-free.md)
- [Double Free (이중 해제)](glossary/double-free.md)
- [Buffer Overflow (버퍼 오버플로우, Heap/Stack)](glossary/buffer-overflow.md)
- [Out-of-Bounds Read/Write (OOB)](glossary/out-of-bounds.md)
- [Type Confusion (타입 컨퓨전)](glossary/type-confusion.md)
- [Integer Overflow/Underflow (정수 오버플로우/언더플로우)](glossary/integer-overflow-underflow.md)

## 동시성/시점 관련

- [Race Condition (레이스 컨디션)](glossary/race-condition.md)
- [TOCTOU](glossary/toctou.md)
- [Symlink Attack (심볼릭 링크 공격)](glossary/symlink-attack.md)

## 입력 검증/인젝션 계열

- [Path Traversal (경로 순회)](glossary/path-traversal.md)
- [Command Injection (명령어 인젝션)](glossary/command-injection.md)
- [SQL Injection (SQL 인젝션)](glossary/sql-injection.md)
- [Format String (형식 문자열 취약점)](glossary/format-string.md)
- [Argument Injection (인자 인젝션)](glossary/argument-injection.md)
- [SSRF](glossary/ssrf.md)
- [XSS (크로스사이트 스크립팅)](glossary/xss.md)
- [Deserialization (역직렬화) 취약점](glossary/deserialization.md)
- [Prototype Pollution (프로토타입 오염)](glossary/prototype-pollution.md)

## 권한/격리

- [Privilege Escalation (권한 상승)](glossary/privilege-escalation.md)
- [Sandbox / Sandbox Escape (샌드박스/샌드박스 탈출)](glossary/sandbox-escape.md)
- [IPC (프로세스 간 통신)](glossary/ipc.md)

## 결과/영향

- [RCE (원격 코드 실행)](glossary/rce.md)

## 분류/채점 체계

- [CVSS](glossary/cvss.md)
- [CWE](glossary/cwe.md)
