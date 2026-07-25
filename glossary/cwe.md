# CWE (Common Weakness Enumeration)

**정의**: 소프트웨어 취약점의 "근본 결함 유형"을 표준화해서 번호를 매겨둔 분류 체계다(예: CWE-416은 Use-After-Free, CWE-89는 SQL Injection). [CVSS](cvss.md)가 "이 취약점이 얼마나 심각한가"를 재는 척도라면, CWE는 "이 취약점이 어떤 종류의 결함인가"를 분류하는 라벨이다.

**어떻게 읽는가**: 리포트나 NVD 항목에서 "CWE-416: Use After Free"처럼 표기된 걸 보면, 이 글로서리의 해당 항목([Use-After-Free](use-after-free.md) 등)으로 바로 연결해서 이해하면 된다. 하나의 CVE에 여러 CWE가 붙는 경우도 있다 — 근본 원인과 그로 인한 결과를 각각 다른 CWE로 분류하기 때문이다.

**참고**: 전체 CWE 목록은 [cwe.mitre.org](https://cwe.mitre.org/)에서 확인할 수 있다. 이 글로서리는 이 저장소 리포트에 실제로 등장하는 유형만 다룬다.
