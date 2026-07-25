# SQL Injection (SQL 인젝션)

**정의**: 사용자 입력을 SQL 쿼리 문자열에 그대로 이어붙일 때, 입력에 SQL 문법 요소(따옴표, `OR`, `;` 등)를 넣어 원래 쿼리의 의미를 바꿔버리는 취약점이다.

**왜 문제가 되는가**: 데이터베이스는 쿼리 문자열 안에서 "이건 사용자가 넣은 값"과 "이건 쿼리 구조"를 구분하지 못한다. 입력이 문자열 결합으로 쿼리에 들어가면, 공격자가 조건문을 조작해 인증을 우회하거나 다른 사용자의 데이터를 통째로 가져올 수 있다.

**간단한 예시**:
```python
# 취약한 코드
query = f"SELECT * FROM users WHERE name = '{username}'"

# username = "' OR '1'='1" 이면
# 실제 쿼리: SELECT * FROM users WHERE name = '' OR '1'='1'  → 전체 사용자 반환
```

**이 저장소의 예**: [CVE-2026-62390](../vulnerability/opensource/CVE-2026-62390-apache-kylin-catalog-cache-refresh-sql-injection.md) (Apache Kylin 카탈로그 캐시 새로고침)
