# Path Traversal (경로 순회, 디렉터리 순회)

**정의**: 사용자가 제공한 파일 경로에 `../` 같은 상위 디렉터리 이동 시퀀스나 절대경로를 끼워넣어, 애플리케이션이 의도한 디렉터리 밖의 파일에 접근하게 만드는 취약점이다.

**왜 문제가 되는가**: 애플리케이션이 "이 폴더 안에서만 파일을 다룬다"고 가정하고 짠 코드가, 그 가정을 검증하지 않고 사용자 입력을 그대로 경로 조합에 쓰면 시스템의 임의 파일을 읽거나 쓸 수 있게 된다.

**간단한 예시**:
```
정상 요청:  GET /files?name=report.pdf   → /var/app/uploads/report.pdf
공격 요청:  GET /files?name=../../etc/passwd → /var/app/uploads/../../etc/passwd → /etc/passwd
```

**이 저장소의 예**: [CVE-2026-9181](../vulnerability/others/CVE-2026-9181-arcgis-uploads-filename-path-traversal.md) (ArcGIS 업로드 파일명), [CVE-2026-53519](../vulnerability/opensource/CVE-2026-53519-nezha-dashboard-prefix-confusion-path-traversal.md) (Nezha Dashboard)
