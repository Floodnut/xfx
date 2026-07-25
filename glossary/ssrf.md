# SSRF (Server-Side Request Forgery, 서버 사이드 요청 위조)

**정의**: 공격자가 서버 애플리케이션을 속여, 서버 자신이 임의의(주로 내부망) URL로 요청을 보내게 만드는 취약점이다. 서버가 대신 요청을 보내주기 때문에, 외부에서는 접근 못 하는 내부 네트워크나 클라우드 메타데이터 엔드포인트에 닿을 수 있다.

**왜 문제가 되는가**: 대부분의 내부 서비스(관리용 API, 클라우드 인스턴스 메타데이터 서버 등)는 "내부망에서 오는 요청이니 신뢰할 수 있다"는 전제로 인증을 약하게 두는 경우가 많다. SSRF는 공격자가 그 신뢰받는 위치에서 요청을 보내는 것처럼 흉내 낼 수 있게 해준다.

**간단한 예시**:
```
정상 사용: POST /fetch-avatar?url=https://cdn.example.com/avatar.png
공격 사용: POST /fetch-avatar?url=http://169.254.169.254/latest/meta-data/  (클라우드 메타데이터 서버)
```

**이 저장소의 예**: [CVE-2022-1592](../vulnerability/opensource/CVE-2022-1592-scout-remote-cors-ssrf.md) (Scout remote_cors), [CVE-2026-22874](../vulnerability/opensource/CVE-2026-22874-gitea-ssrf-allowlist-reserved-ranges.md) (Gitea)
