# xfx

## 마지막 업데이트

| 일자 | 리포트 | 분류 | 내용 |
| --- | --- | --- | --- |
| 2026-08-27 | [CVE-2026-28291](vulnerability/opensource/CVE-2026-28291-simple-git-option-parsing-command-execution.md) | Opensource | simple-git의 부분적인 -u 정규식 검사가 Git의 짧은 옵션 묶음 해석을 놓쳐 위험한 upload-pack 인자를 통과시킨 문제이며, 3.32.0이 묶음 안의 u를 검사하도록 보완했다. |
| 2026-08-27 | [Cilium Gateway ListenerSet Delegation](oss-changes/cilium/1.20-cilium-gateway-listenerset-delegation.md) | OSS / Cilium | 공유 Gateway의 listener 소유권을 ListenerSet으로 위임하고, Cilium이 병합된 Envoy 설정과 리소스별 TLS 권한 경계를 함께 유지 |
| 2026-08-27 | [Envoy CPU-local Connection Steering](oss-changes/envoy/1.39-envoy-cpu-local-connection-steering.md) | OSS / Envoy | accept마다 mutex를 잡는 정확한 균형 대신 SO_REUSEPORT BPF가 수신 CPU에 고정된 worker socket으로 연결을 보내는 Linux TCP 설계 |

## CVE 취약점 분석

<details>
<summary>Linux (36)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2014-6271](vulnerability/linux/CVE-2014-6271-shellshock.md) | Shellshock — Bash 함수 정의 파싱이 끝나지 않아 뒤에 붙은 명령까지 실행되는 취약점 |
| [CVE-2014-7169](vulnerability/linux/CVE-2014-7169-shellshock-incomplete-patch.md) | Shellshock 최초 패치 이후에도 파서가 lookahead 문자를 초기화하지 않아 뒷부분이 재해석되던 후속 취약점 |
| [CVE-2018-15686](vulnerability/linux/CVE-2018-15686-systemd-notify-reexec-state-injection.md) | systemd 재실행 시 상태 역직렬화 스택 버퍼 오버플로우 |
| [CVE-2018-19788](vulnerability/linux/CVE-2018-19788-polkit-uid-int-overflow.md) | polkit이 UID를 부호 있는 정수로 다뤄 INT_MAX 초과 UID가 root로 오인되는 취약점 |
| [CVE-2019-13272](vulnerability/linux/CVE-2019-13272-ptrace-traceme-cred.md) | `ptrace_link()`가 잘못된 프로세스의 자격증명을 기록해 pkexec와 결합 시 권한 상승 |
| [CVE-2019-18276](vulnerability/linux/CVE-2019-18276-bash-disable-priv-mode.md) | Bash disable_priv_mode()가 saved-UID를 안 지워서 setuid 권한이 남는 취약점 |
| [CVE-2021-3560](vulnerability/linux/CVE-2021-3560-polkit-dbus-race.md) | polkit이 D-Bus 조회 실패를 root(UID 0)로 오인하는 레이스 컨디션 |
| [CVE-2021-4034](vulnerability/linux/CVE-2021-4034-pwnkit.md) | PwnKit — pkexec가 argc=0 실행을 예상 못해 환경변수를 인자로 오인 |
| [CVE-2022-0847](vulnerability/linux/CVE-2022-0847-dirty-pipe.md) | Dirty Pipe — 파이프 버퍼 flags 미초기화로 읽기 전용 파일 덮어쓰기 |
| [CVE-2022-1015](vulnerability/linux/CVE-2022-1015-nf-tables-register-overflow.md) | nf_tables 레지스터 번호 검증의 32비트 정수 오버플로우 |
| [CVE-2023-0179](vulnerability/linux/CVE-2023-0179-nft-payload-vlan-overflow.md) | nft_payload VLAN 헤더 처리의 정수 언더플로우로 인한 스택 버퍼 오버플로우 |
| [CVE-2023-32233](vulnerability/linux/CVE-2023-32233-nf-tables-anon-set-uaf.md) | nf_tables 배치 트랜잭션에서 익명 집합 비활성화 누락으로 인한 UAF |
| [CVE-2023-4147](vulnerability/linux/CVE-2023-4147-nftables-bound-chain-rule-injection-uaf.md) | 바인딩된 체인에 트랜잭션 로컬 ID로 규칙을 몰래 추가할 수 있는 검사 우회 |
| [CVE-2024-28085](vulnerability/linux/CVE-2024-28085-wall-escape-sequence-injection.md) | util-linux `wall`이 argv 경로만 이스케이프 필터링을 안 해 생긴 터미널 인젝션 |
| [CVE-2025-32463](vulnerability/linux/CVE-2025-32463-sudo-chroot-nsswitch.md) | sudo `--chroot`가 정책 검사보다 먼저 일어나 공격자의 nsswitch.conf를 신뢰하는 취약점 |
| [CVE-2025-6018](vulnerability/linux/CVE-2025-6018-pam-env-allow-active-spoof.md) | PAM pam_env로 SSH 세션을 물리 콘솔 세션처럼 속여 allow_active 권한 탈취 |
| [CVE-2026-28372](vulnerability/linux/CVE-2026-28372-telnetd-systemd-credentials-noauth-bypass.md) | util-linux 2.40의 systemd 자격증명 지원(login.noauth/CREDENTIALS_DIRECTORY)을 GNU inetutils telnetd가 클라이언트 환경변수를 무검증으로 전달하며 그대로 신뢰해버려, 로컬 사용자가 텔넷 접속만으로 root 인증을 건너뛸 수 있었다. |
| [CVE-2006-5051](vulnerability/linux/CVE-2006-5051-openssh-sigalrm-cleanup-double-free.md) | OpenSSH sshd의 로그인 유예시간 알람(SIGALRM) 핸들러가 인증 완료 여부를 구분하지 않고 비동기 시그널 불안전한 정리 함수(fatal/syslog, GSSAPI 정리)를 호출해 이중 해제로 이어질 수 있었던 경쟁 조건으로, 이 설계 결함은 18년 뒤 CVE-2024-6387로 재발했다. |
| [CVE-2018-16865](vulnerability/linux/CVE-2018-16865-systemd-journald-alloca-stack-clash.md) | systemd-journald의 네이티브 로그 프로토콜이 항목당 필드 개수에 상한을 두지 않아, journal_file_append_entry()가 필드 수에 비례한 크기(최대 약 4GB)를 검사 없이 alloca()로 할당하면서 스택이 인접 메모리 영역과 충돌(Stack Clash)해 DoS/코드 실행으로 이어질 수 있었던 취약점. |
| [CVE-2015-5602](vulnerability/linux/CVE-2015-5602-sudoedit-symlink-parent-dir-check-bypass.md) | sudoedit의 심볼릭 링크 방지 검사가 파일 바로 위 디렉터리 한 단계만 확인해, sudoers에 다중 와일드카드 경로를 쓰면 상위 디렉터리의 심볼릭 링크로 우회할 수 있었던 문제(1.8.15에서 최초 도입, 1.8.16에서 전체 경로 순회로 재작성) |
| [CVE-2002-0639](vulnerability/linux/CVE-2002-0639-openssh-challenge-response-integer-overflow.md) | OpenSSH의 keyboard-interactive 인증에서 클라이언트가 주장하는 응답 개수를 검증 없이 배열 크기 계산에 곱해, 정수 오버플로로 작게 할당된 힙 버퍼 너머로 원격 root 권한 쓰기가 가능했던 취약점. |
| [CVE-2013-1775](vulnerability/linux/CVE-2013-1775-sudo-epoch-timestamp-bypass.md) | sudo -k가 타임스탬프를 삭제 대신 epoch로 리셋하는 설계 때문에, 인증 이력이 있는 로컬 사용자가 시스템 시계를 epoch로 되돌리기만 하면 재인증 없이 sudo를 계속 쓸 수 있었던 인증 우회 취약점. |
| [CVE-2010-3847](vulnerability/linux/CVE-2010-3847-glibc-ld-audit-origin-privesc.md) | glibc ld.so가 setuid 프로그램에서 $ORIGIN 단독 사용만 예외로 허용하던 버그를 LD_AUDIT=$ORIGIN과 하드링크로 결합해 임의 공유 오브젝트를 로드시켜 루트 권한을 얻는 CVE-2010-3847을 분석했다. |
| [CVE-2026-53359](vulnerability/linux/CVE-2026-53359-kvm-shadow-paging-role-uaf.md) | KVM shadow paging의 GFN만 비교한 child shadow page 재사용이 role 불일치와 stale rmap을 만들어 Use-After-Free로 이어지는 경로와 role 비교 패치를 설명한다. |
| [CVE-2026-46113](vulnerability/linux/CVE-2026-46113-kvm-shadow-paging-gfn-uaf.md) | KVM/x86 shadow paging에서 kvm_mmu_get_child_sp()가 child shadow page의 GFN을 재검증하지 않아, 게스트 페이지 테이블이 VM 진입 사이 바뀌면 stale rmap이 남아 해제된 shadow page를 참조하는 Use-After-Free가 발생한다. |
| [CVE-2008-0166](vulnerability/linux/CVE-2008-0166-debian-openssl-predictable-prng.md) | Debian OpenSSL 패치 실수로 PRNG 엔트로피가 PID 값 하나로 축소된 이슈 |
| [CVE-2003-0127](vulnerability/linux/CVE-2003-0127-ptrace-kmod-kernel-thread-race.md) | Linux 2.2/2.4 커널의 kmod 모듈 자동 로더가 만드는 root 권한 커널 스레드가 ptrace 보호 사각지대에 있어, 로컬 공격자가 modprobe 실행 순간에 셸코드를 주입해 root 권한을 획득할 수 있었다. |
| [CVE-2012-0864](vulnerability/linux/CVE-2012-0864-glibc-vfprintf-nargs-integer-overflow.md) | glibc vfprintf()의 위치 지정 인자 개수 곱셈이 32비트에서 오버플로해 FORTIFY_SOURCE 검사 배열 밖 쓰기와 형식 문자열 보호 우회를 허용했다. |
| [CVE-2018-12562](vulnerability/linux/CVE-2018-12562-cantata-mounter-unquoted-argv-glob-expansion.md) | Cantata의 root D-Bus mounter가 Bash 래퍼의 따옴표 없는 $@ 때문에 한 개의 마운트 지점 인자를 로컬 파일명 여러 개로 재확장하던 문제다. |
| [CVE-2018-12559](vulnerability/linux/CVE-2018-12559-cantata-mounter-lexical-home-prefix-path-traversal.md) | Cantata의 root D-Bus mounter는 마운트 지점을 정규화하지 않고 /home/ 문자열 접두사만 검사해 일반 사용자가 홈 밖 위치에 CIFS 공유를 마운트하거나 해제할 수 있었다. |
| [CVE-2026-64561](vulnerability/linux/CVE-2026-64561-kvm-recursive-zap-active-list-uaf.md) | 중첩 가상화에서 KVM/x86 shadow MMU의 재귀 zap이 사용 중인 root를 무효화한 뒤에도 매핑을 계속해 invalid child를 활성 목록에 넣고, 해제 후 list_add가 stale link에 쓰는 Use-After-Free 경로와 Zapscape의 Intel/AMD 조건을 설명한다. |
| [CVE-2026-46316](vulnerability/linux/CVE-2026-46316-kvm-arm64-vgic-its-translation-cache-uaf.md) | KVM arm64의 VGIC ITS 번역 캐시가 경쟁 중 같은 IRQ 참조를 두 번 해제해 Use-After-Free를 일으키는 원인과 xa_erase 반환값을 사용하는 수정 방법을 설명한다. |
| [CVE-2021-3996](vulnerability/linux/CVE-2021-3996-libmount-fuse-unmount-permission-bypass.md) | util-linux libmount의 FUSE 언마운트 권한 검사 함수 is_fuse_usermount()에 있던 두 논리 오류(CVE-2021-3996, CVE-2021-3995)로 저권한 로컬 사용자가 다른 사용자 소유 파일시스템을 강제로 언마운트해 서비스 거부를 일으킬 수 있었던 취약점을 분석했다. |
| [CVE-2021-3995](vulnerability/linux/CVE-2021-3995-util-linux-libmount-fuse-uid-prefix-unmount.md) | util-linux libmount가 FUSE 마운트의 user_id를 문자열 접두사로 비교해 다른 사용자의 파일시스템 해제를 허용한 문제를 UID 정수 파싱과 정확한 비교로 수정한 과정을 설명한다. |
| [CVE-2018-1049](vulnerability/linux/CVE-2018-1049-systemd-automount-race-mountpoint-hang.md) | CVE-2018-1049는 systemd의 .mount 완료가 커널 autofs 요청보다 먼저 처리될 때 늦은 요청 토큰에 READY ACK를 보내지 않아 mountpoint 접근을 멈추게 한 race이며, 수정은 이미 마운트된 분기에서 토큰을 성공 처리하고 반환한다. |
| [CVE-2023-3390](vulnerability/linux/CVE-2023-3390-nf-tables-anonymous-set-uaf.md) | Linux nf_tables가 익명 세트를 참조하는 규칙 추가 오류 경로에서 NFT_TRANS_RELEASE로 세트를 먼저 해제해 dangling pointer를 만든 원인과 NFT_TRANS_PREPARE 수정 과정을 설명한다. |

</details>

<details>
<summary>Opensource (42)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2019-5736](vulnerability/opensource/CVE-2019-5736-runc-proc-self-exe-escape.md) | runc가 `/proc/self/exe`를 통해 호스트 바이너리를 덮어쓸 수 있는 컨테이너 탈출 |
| [CVE-2026-0770](vulnerability/opensource/CVE-2026-0770-langflow-validate-code-exec-rce.md) | Langflow의 코드 "검증" 엔드포인트가 실제로는 `exec()`로 임의 코드를 실행하던 취약점 |
| [CVE-2019-11358](vulnerability/opensource/CVE-2019-11358-jquery-extend-prototype-pollution.md) | jQuery의 $.extend(true, ...) 깊은 병합 로직이 속성 이름 __proto__를 일반 키와 구분하지 않아, 공격자가 넣은 데이터가 재귀적으로 전역 공유 객체인 Object.prototype 자체를 오염시킬 수 있었다. |
| [CVE-2020-8165](vulnerability/opensource/CVE-2020-8165-rails-cache-raw-marshal-load-rce.md) | Rails의 MemCacheStore/RedisCacheStore가 raw: true로 저장된(Marshal 직렬화를 거치지 않은) 캐시 값도 읽을 때 구분 없이 Marshal.load를 먼저 시도해, 공격자가 raw 캐시 값에 심은 Marshal 페이로드가 역직렬화되어 원격 코드 실행으로 이어질 수 있었던 취약점. |
| [CVE-2019-20372](vulnerability/opensource/CVE-2019-20372-nginx-error-page-request-smuggling.md) | nginx가 error_page를 외부 URL로 리다이렉트할 때 아직 읽지 않은 요청 바디를 폐기하지 않아, keep-alive 연결에서 그 바디가 다음 파이프라인 요청으로 오인되어 로드밸런서의 접근 제어를 우회하는 HTTP 요청 밀수가 가능했던 문제 |
| [CVE-2018-16843](vulnerability/opensource/CVE-2018-16843-nginx-http2-frame-flood-memory-exhaustion.md) | nginx HTTP/2가 SETTINGS/PING 확인 응답 프레임 할당 개수에 상한을 두지 않아, 클라이언트가 응답을 읽지 않으면서 프레임을 계속 보내면 워커 프로세스 메모리가 무제한으로 늘어날 수 있었던 취약점. |
| [CVE-2026-33264](vulnerability/opensource/CVE-2026-33264-airflow-trigger-deserialize-rce.md) | Airflow가 직렬화된 DAG의 트리거 kwargs를 검증 없이 역직렬화하며 import_string으로 임의 클래스를 인스턴스화해, DAG 작성자가 스케줄러/API 서버(더 높은 신뢰 등급)에서 원격 코드를 실행할 수 있었던 Critical 취약점. |
| [CVE-2018-12326](vulnerability/opensource/CVE-2018-12326-redis-cli-buffer-overflow.md) | redis-cli가 snprintf 반환값을 실제로 쓴 바이트 수로 착각해 다음 오프셋 계산에 재사용하면서, 긴 -h 호스트명 하나로 정수 언더플로우를 유발해 128바이트 프롬프트 버퍼를 넘어서는 스택 버퍼 오버플로우가 발생하는 문제. |
| [CVE-2022-1592](vulnerability/opensource/CVE-2022-1592-scout-remote-cors-ssrf.md) | Scout의 remote_cors가 사용자 제공 URL을 서버 측 requests.request()로 그대로 프록시해 SSRF가 가능했던 문제를, 인증된 세션의 IGV 트랙 허용 목록으로 목적지 권한을 묶어 해결한 취약점. |
| [CVE-2026-28292](vulnerability/opensource/CVE-2026-28292-simple-git-protocol-allow-case-bypass.md) | simple-git의 protocol.allow 차단기가 Git 설정 키의 대소문자 비구분 규칙을 놓쳐 외부 helper 실행 경로를 열었던 취약점이다. |
| [CVE-2006-3918](vulnerability/opensource/CVE-2006-3918-apache-expect-header-reflected-xss.md) | Apache HTTP Server가 417 Expectation Failed 에러 페이지에 클라이언트가 보낸 Expect 헤더 값을 HTML 이스케이프 없이 그대로 반사해, 임의 헤더 전송이 가능한 클라이언트를 통해 반사형 XSS가 가능했던 문제 |
| [CVE-2026-62390](vulnerability/opensource/CVE-2026-62390-apache-kylin-catalog-cache-refresh-sql-injection.md) | Apache Kylin의 카탈로그 캐시 새로고침이 테이블 이름을 SQL에 그대로 이어 붙인 문제와 식별자 allowlist 패치를 분석한다. |
| [CVE-2026-62392](vulnerability/opensource/CVE-2026-62392-apache-kylin-async-query-os-command-injection.md) | Apache Kylin 비동기 쿼리의 YARN 큐 이름을 검증 없이 spark-submit 셸 명령에 작은따옴표로 감싸 이어붙이던 CVE-2026-62392를 분석했다 — 값에 작은따옴표 하나만 넣으면 인용 구간이 끊겨 임의 OS 명령이 실행된다. |
| [CVE-2026-53519](vulnerability/opensource/CVE-2026-53519-nezha-dashboard-prefix-confusion-path-traversal.md) | Nezha Monitoring의 관리자 정적 파일 fallback에서 문자열 접두사 검사와 경로 정규화가 결합해 인증 전 경로 순회와 JWT 키 노출로 이어진 원리를 분석한다. |
| [CVE-2022-46292](vulnerability/opensource/CVE-2022-46292-openbabel-mopac-translation-vector-stack-overflow.md) | Open Babel MOPAC 파서가 UNIT CELL TRANSLATION 벡터 개수를 검증하지 않아 고정 크기 스택 배열(translationVectors[3])을 넘어 계속 쓰는 스택 버퍼 오버플로우 |
| [CVE-2026-9090](vulnerability/opensource/CVE-2026-9090-casdoor-saml-response-certificate-trust-confusion.md) | Casdoor가 SAML 응답에 포함된 공격자 인증서를 신뢰 저장소로 사용해 위조 assertion을 검증할 수 있었던 인증 우회 취약점 분석. |
| [CVE-2026-27771](vulnerability/opensource/CVE-2026-27771-gitea-composer-source-link-permission-bypass.md) | Gitea Composer 레지스트리가 패키지에 링크된 저장소를 응답에 넣을 때 요청자의 실제 저장소 접근 권한을 확인하지 않아, 공개 패키지에 링크된 비공개/내부 저장소의 존재와 URL이 인증 없이 노출됐다(1.26.2에서 수정). |
| [CVE-2004-0836](vulnerability/opensource/CVE-2004-0836-mysql-real-connect-dns-hlength-overflow.md) | MySQL 클라이언트 mysql_real_connect()가 DNS 응답의 h_length를 검증 없이 memcpy 길이로 써 sockaddr_in.sin_addr 고정 버퍼를 넘길 수 있었던 2004년 스택 오버플로 취약점 분석 |
| [CVE-2026-35355](vulnerability/opensource/CVE-2026-35355-uutils-install-toctou-symlink.md) | uutils coreutils install이 대상 파일을 unlink 후 O_EXCL 없이 경로 이름으로 재생성해, 그 틈에 심볼릭 링크를 심으면 root 권한으로 임의 시스템 파일을 덮어쓸 수 있는 TOCTOU 레이스가 존재했다. |
| [CVE-2026-35341](vulnerability/opensource/CVE-2026-35341-uutils-mkfifo-missing-continue.md) | uutils coreutils mkfifo가 FIFO 생성 실패 시 continue 문이 빠져 있어, 이미 존재하던 파일(예: SSH 개인키)의 권한을 실수로 기본 모드로 덮어써 노출시킬 수 있었다. |
| [CVE-2026-22874](vulnerability/opensource/CVE-2026-22874-gitea-ssrf-allowlist-reserved-ranges.md) | Gitea 웹훅/마이그레이션의 SSRF 허용목록이 net.IP.IsPrivate()에만 의존해 클라우드 메타데이터·CGNAT·NAT64 등 예약 대역을 걸러내지 못했다 |
| [CVE-2026-13676](vulnerability/opensource/CVE-2026-13676-fast-uri-idn-canonicalization-bypass.md) | fast-uri가 존재하지 않는 URL.domainToASCII를 호출해 IDN 호스트 정규화에 실패, 표준 URL/fetch와 다른 호스트를 반환한다 |
| [CVE-2026-57516](vulnerability/opensource/CVE-2026-57516-ray-webdataset-pickle-torch-load-rce.md) | Ray의 read_webdataset 기본 디코더가 .pkl/.pt 파일을 검증 없이 pickle.loads/torch.load로 역직렬화해, 신뢰할 수 없는 WebDataset TAR를 미리보기만 해도 Ray 워커에서 임의 코드가 실행될 수 있었다. |
| [CVE-2026-50524](vulnerability/opensource/CVE-2026-50524-dotnet-sslstream-malformed-tls-frame-dos.md) | .NET SslStream이 malformed TLS 후속 헤더의 -1 길이를 정상값처럼 소비해 버퍼 진행을 역전시키고 원격 서비스 거부를 일으켰다. |
| [CVE-2002-0969](vulnerability/opensource/CVE-2002-0969-mysql-win32-datadir-buffer-overflow.md) | MySQL Win32 mysqld-nt 서비스가 SYSTEM 권한으로 my.ini의 datadir 값을 길이 검사 없는 strmov로 512바이트 고정 전역 버퍼에 복사해, 느슨한 파일 ACL과 결합하면 로컬 사용자가 SYSTEM 권한 코드 실행까지 이어질 수 있었다. |
| [CVE-2007-3280](vulnerability/opensource/CVE-2007-3280-postgresql-dblink-arbitrary-library-function-mapping.md) | PostgreSQL의 dblink 모듈과 기본 local trust 인증을 조합하면 저권한 사용자가 슈퍼유저로 재접속해 임의 공유 라이브러리 함수를 SQL 함수로 매핑할 수 있었고, libc의 system()을 매핑해 셸 명령 실행까지 도달할 수 있었다. |
| [CVE-2026-50559](vulnerability/opensource/CVE-2026-50559-quarkus-http-path-normalization-auth-bypass.md) | Quarkus HTTP 경로 정책이 부분 디코딩 경로를 검사한 뒤 후단 핸들러가 예약 문자를 추가로 해석해 보호 엔드포인트와 정적 자원에 대한 인증을 우회할 수 있었다. |
| [CVE-2005-2088](vulnerability/opensource/CVE-2005-2088-apache-proxy-te-cl-request-smuggling.md) | Apache mod_proxy_http TE/CL request smuggling analysis |
| [CVE-2007-4772](vulnerability/opensource/CVE-2007-4772-tcl-regex-nfa-error-propagation-infinite-loop.md) | Tcl regex NFA error propagation infinite loop analysis |
| [CVE-2005-0490](vulnerability/opensource/CVE-2005-0490-curl-authentication-base64-stack-buffer-overflow.md) | curl의 NTLM·Kerberos v4 인증 응답을 고정 버퍼에 길이 검증 없이 Base64 디코드하던 경로와 동적 할당·상한 검사 패치 체인을 설명한다. |
| [CVE-2026-33278](vulnerability/opensource/CVE-2026-33278-unbound-dnssec-deepcopy-dangling-rrsets.md) | Unbound가 NSEC3 계산 예산 고갈로 DNSSEC 검증을 중단할 때 응답 메시지를 상위 쿼리 리전으로 깊은 복사하면서 구조체를 통째로 대입해 RRset 포인터 배열이 곧 해제될 서브쿼리 리전을 가리킨 채 남았고, 검증 재개 시 그 배열을 다시 읽어 Use-After-Free가 발생한다. |
| [CVE-2025-68616](vulnerability/opensource/CVE-2025-68616-weasyprint-default-url-fetcher-redirect-ssrf-bypass.md) | WeasyPrint 68.0 이전 default_url_fetcher가 내부 urllib 리다이렉트를 자동 추적해 사용자 정의 호스트 차단 정책을 우회하던 SSRF를 allow_redirects=False로 수정한 CVE-2025-68616 분석 |
| [CVE-2019-14271](vulnerability/opensource/CVE-2019-14271-docker-tar-nss-chroot-code-injection.md) | Docker 19.03.0의 docker-tar가 컨테이너 rootfs로 전환한 뒤 glibc NSS를 처음 로드해 컨테이너의 libnss 공유 라이브러리를 호스트 측 프로세스에서 실행할 수 있었고, 19.03.1은 chroot 전에 NSS를 초기화한다. |
| [CVE-2018-15664](vulnerability/opensource/CVE-2018-15664-docker-cp-followsymlinkinscope-toctou-symlink-race.md) | Docker docker cp에서 FollowSymlinkInScope 해석 이후 컨테이너가 심볼릭 링크를 스왑하면 chrootarchive가 호스트 파일시스템을 대상으로 tar 풀기를 실행하는 TOCTOU 취약점 |
| [CVE-2017-7529](vulnerability/opensource/CVE-2017-7529-nginx-range-filter-integer-overflow-info-disclosure.md) | nginx Range 필터가 여러 구간의 전체 길이를 누적하는 과정에서 정수 오버플로우를 허용해 캐시 파일 헤더 등 민감 정보가 노출될 수 있었고, 사전 덧셈 상한 검사로 수정된 과정을 분석했다. |
| [CVE-2013-2028](vulnerability/opensource/CVE-2013-2028-nginx-chunked-transfer-encoding-stack-overflow.md) | nginx 1.3.9~1.4.0의 청크 전송 인코딩 파서가 청크 크기를 상한 검사 없이 누적하다 정수 오버플로우로 음수가 되면, 이 값이 discard body 경로의 recv() 길이 인자로 재해석되어 4096바이트 스택 버퍼를 넘겨 쓰는 스택 버퍼 오버플로우로 이어진다. |
| [CVE-2018-16864](vulnerability/opensource/CVE-2018-16864-systemd-journald-commandline-stack-allocation.md) | systemd-journald가 길이를 제어할 수 있는 명령줄을 스택에서 조합해 충돌할 수 있었고, 수정은 두 명령줄 필드를 힙 할당으로 전환한다. |
| [CVE-2013-4547](vulnerability/opensource/CVE-2013-4547-nginx-uri-space-parser-restriction-bypass.md) | nginx는 URI 안 공백 뒤 첫 문자를 건너뛰어 URI 정규화와 설정 기반 제한을 다르게 적용할 수 있었고, 수정은 두 분기에 p--를 추가해 그 문자를 재처리한다. |
| [CVE-2015-4335](vulnerability/opensource/CVE-2015-4335-redis-lua-bytecode-sandbox-escape.md) | Redis EVAL의 Lua loader가 공격자 제어 binary chunk를 luaU_undump로 받아들여 sandbox 경계를 벗어나던 경로와, source parser만 사용하도록 바꾼 수정 과정을 분석한다. |
| [CVE-2015-8080](vulnerability/opensource/CVE-2015-8080-redis-lua-struct-integer-overflow.md) | Redis Lua struct의 getnum() 정수 범위 미검사로 비정상 크기가 고정 버퍼 직렬화로 전달되는 경로와 2.8.24, 3.0.6, 5.0.8의 수정 과정을 설명한다. |
| [CVE-2026-54917](vulnerability/opensource/CVE-2026-54917-seaweedfs-s3-iceberg-cross-bucket-path-traversal.md) | SeaweedFS 4.30 미만의 S3와 Iceberg gateway가 SkipClean(true)로 보존한 경로 변수를 후속 경로 조합에서 재해석해 버킷 경계를 우회한 문제와 handler 전 검증 middleware 수정 과정을 설명한다. |
| [CVE-2026-28291](vulnerability/opensource/CVE-2026-28291-simple-git-option-parsing-command-execution.md) | simple-git의 부분적인 -u 정규식 검사가 Git의 짧은 옵션 묶음 해석을 놓쳐 위험한 upload-pack 인자를 통과시킨 문제이며, 3.32.0이 묶음 안의 u를 검사하도록 보완했다. |

</details>

<details>
<summary>Windows (7)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2023-23397](vulnerability/windows/CVE-2023-23397-outlook-ntlm-reminder.md) | Outlook reminder 처리 중 사용자 상호작용 없이 NTLM 해시가 유출되는 권한 상승 |
| [CVE-2025-33073](vulnerability/windows/CVE-2025-33073-windows-smb-client-ntlm-reflection.md) | Windows SMB Client가 marshalled target info가 붙은 특수 대상 이름을 로컬 호스트로 오판해, SMB signing이 강제되지 않은 환경에서 NTLM reflection을 SYSTEM 권한 작업으로 확장할 수 있는 취약점이다. |
| [CVE-2025-21333](vulnerability/windows/CVE-2025-21333-windows-hyperv-crossvmevent-heap-overflow.md) | Hyper-V NT Kernel Integration VSP의 CrossVmEvent 생성 경로에서 발생한 heap overflow가 WNF/I/O ring 기반 커널 객체 손상과 로컬 권한 상승으로 이어질 수 있는 취약점이다. |
| [CVE-2026-40369](vulnerability/windows/CVE-2026-40369-windows-kernel-pointer-overflow.md) | Windows Kernel이 신뢰할 수 없는 포인터와 길이 정보를 잘못 다룰 때 커널 풀 손상과 제한적 SYSTEM 권한 상승으로 이어질 수 있는 로컬 취약점이다. |
| [CVE-2022-21882](vulnerability/windows/CVE-2022-21882-win32k-window-object-type-confusion.md) | Win32k user-mode callback 뒤 창 객체 표현을 재검증하지 않아 생긴 타입 혼동과 ConsoleWindow 상태 검증 패치를 분석한다. |
| [CVE-2025-5986](vulnerability/windows/CVE-2025-5986-thunderbird-mailbox-unc-credential-leak.md) | Thunderbird의 mailbox: UNC 처리 경계를 보강해 무단 다운로드·디스크 소진·Windows SMB 자격 증명 노출 가능성을 차단한 CVE-2025-5986 분석. |
| [CVE-2025-50168](vulnerability/windows/CVE-2025-50168-win32k-directcomposition-shared-resource-type-confusion.md) | CVE-2025-50168은 DirectComposition 공유 리소스의 타입 태그와 실제 marshaler 객체 레이아웃 불일치가 속성 setter의 범위 밖 쓰기로 이어진 Windows 권한 상승 취약점이다. |

</details>

<details>
<summary>Browser (10)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2026-56645](vulnerability/browser/CVE-2026-56645-edge-heap-buffer-overflow.md) | Microsoft Edge(Chromium 기반) 힙 버퍼 오버플로우 |
| [CVE-2026-57572](vulnerability/browser/CVE-2026-57572-crawl4ai-chromium-argument-injection.md) | Crawl4AI가 요청 값을 Chromium 실행 인자로 그대로 넘겨 발생한 원격 코드 실행 |
| [CVE-2026-58289](vulnerability/browser/CVE-2026-58289-edge-type-confusion.md) | Microsoft Edge(Chromium 기반) type confusion |
| [CVE-2026-15113](vulnerability/browser/CVE-2026-15113-chrome-android-autofill-uaf.md) | Chrome Android Autofill의 renderer-browser form 상태와 객체 수명 관리가 어긋나 sandbox escape 가능성으로 이어질 수 있는 use-after-free 취약점이다. |
| [CVE-2026-15719](vulnerability/browser/CVE-2026-15719-firefox-dom-navigation-site-isolation.md) | Firefox의 교차 프로세스 탐색에서 문서·IPC actor 교체가 site isolation 보안 경계를 이루는 방식을 설명하고, 비공개 결함 세부는 제한적 모델로 구분한다. |
| [CVE-2026-14906](vulnerability/browser/CVE-2026-14906-firefox-ios-pdf-title-path-overwrite.md) | 악성 웹 페이지 제목이 PDF 저장 경로에 영향을 주며 Firefox for iOS 앱 샌드박스 안의 기존 PDF 또는 번들 콘텐츠를 덮어쓸 수 있었던 문제를 파일명 정규화와 저장 경계 관점에서 분석한다. |
| [CVE-2026-15718](vulnerability/browser/CVE-2026-15718-firefox-webassembly-invalid-pointer.md) | Firefox WebAssembly의 모듈·인스턴스·메모리 수명 모델을 통해 invalid pointer 경계가 왜 중요한지 설명한 분석 |
| [CVE-2025-31277](vulnerability/browser/CVE-2025-31277-javascriptcore-jit-type-confusion.md) | JavaScriptCore JIT의 타입 가정이 실제 값 표현과 어긋날 때 메모리 손상으로 이어지는 원리와 DarkSword 초기 RCE 단계에서의 역할을 분석한다. |
| [CVE-2025-2783](vulnerability/browser/CVE-2025-2783-chrome-mojo-pseudo-handle-sandbox-escape.md) | Windows 의사 핸들이 Mojo/ipcz 경계를 건너 수신자 권한으로 재해석될 수 있었던 논리 오류와 다중 경계 차단 패치를 분석한다. |
| [CVE-2026-15772](vulnerability/browser/CVE-2026-15772-chrome-android-gpu-fbo-uaf-sandbox-escape.md) | Chrome Android GPU 읽기 경로가 임시 FBO를 해제하기 전에 바인딩을 복원하도록 고쳐 드라이버 내부 UAF와 잠재적 샌드박스 탈출을 차단한 CVE-2026-15772 분석. |

</details>

<details>
<summary>Others (4)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2025-43300](vulnerability/others/CVE-2025-43300-apple-imageio-dng-oob-write.md) | Apple ImageIO의 DNG lossless JPEG 디코더가 SamplesPerPixel과 NumComponents 불일치로 출력 버퍼 범위를 넘어 쓰는 원리와 버퍼 경계 검사 패치를 분석한다. |
| [CVE-2026-9181](vulnerability/others/CVE-2026-9181-arcgis-uploads-filename-path-traversal.md) | ArcGIS Server 12.0 이하의 UploadsManager가 클라이언트 파일명을 상대 경로 검사 없이 쓰기 경로에 결합해 업로드 루트 밖의 민감한 설정 파일을 덮어쓸 수 있는 취약점이다. |
| [CVE-2025-6428](vulnerability/others/CVE-2025-6428-firefox-android-link-parameter-open-redirect.md) | Firefox for Android가 일반 URL의 link 매개변수를 앱 링크 대체 목적지로 신뢰해 피싱 경로를 만들던 회귀와 Firefox 140의 수정 분석 |
| [CVE-2024-44308](vulnerability/others/CVE-2024-44308-javascriptcore-dfg-jit-register-state.md) | JavaScriptCore DFG JIT가 느린 경로를 기록한 뒤 임시 레지스터를 할당해 전역 레지스터 상태가 어긋날 수 있었던 문제를, 할당을 느린 경로 이전으로 옮겨 두 제어 흐름의 상태를 일치시킨 수정 분석 |

</details>

## OSS 아키텍처 변경 분석

<details>
<summary>Linux Kernel (5)</summary>

| 변경 | 내용 |
| --- | --- |
| [Live Update Orchestrator / KHO](oss-changes/linux-kernel/6.19-live-update-orchestrator-kexec-hypervisor.md) | kexec로 VM을 안 끄고 커널을 업데이트하는 프레임워크 — KHO의 radix tree/FDT 기반 메모리 보존과 LUO의 콜백 기반 자원 생명주기 관리 |
| [Linux Kernel pidfd Process Lifecycle: CLONE_AUTOREAP/CLONE_PIDFD_AUTOKILL](oss-changes/linux-kernel/7.1-pidfd-process-lifecycle-autoreap-autokill.md) | clone3()에 CLONE_AUTOREAP/CLONE_PIDFD_AUTOKILL 플래그를 추가해, 부모 전체에 걸리던 SIGCHLD 기반 auto-reap을 자식 단위로 세분화하고 pidfd 소유권에 자식 생명주기를 묶었다. |
| [Linux Kernel NTFS Driver Resurrection](oss-changes/linux-kernel/7.1-ntfs-driver-resurrection-iomap-rewrite.md) | 제거됐던 read-only NTFS 드라이버가 4년의 재작성을 거쳐 iomap/folio 기반 쓰기 지원 드라이버로 ntfs3와 나란히 공존하며 부활했다. |
| [Linux Kernel FRED Enabled by Default](oss-changes/linux-kernel/7.1-linux-kernel-fred-enabled-by-default.md) | 6.9에 병합되고도 fred=on 없이는 쓰이지 않던 FRED가 7.1에서 기본값이 되면서, x86-64의 기본 이벤트 전달 경로가 IDT에서 FRED로 넘어갔다. |
| [Linux Kernel 캐시 인지 로드 밸런싱](oss-changes/linux-kernel/7.2-linux-kernel-cache-aware-load-balancing.md) | 프로세스별 LLC 선호를 load-balance 이주에 연결 |

</details>

<details>
<summary>Kubernetes (5)</summary>

| 변경 | 내용 |
| --- | --- |
| [In-Place Pod Resize GA (KEP-1287)](oss-changes/kubernetes/1.35-in-place-pod-resize-ga.md) | Pod 재시작 없이 CPU/메모리를 바꾸는 기능이 v1.35에서 GA — Desired/Allocated/Actuated/Actual 4단계 상태 기계 |
| [Server-Side Sharded List/Watch](oss-changes/kubernetes/1.36-server-side-sharded-list-and-watch.md) | shardSelector로 LIST/WATCH 필터링을 API 서버(워치 캐시)로 옮겨 컨트롤러 수평 확장 시 레플리카 수에 비례해 커지던 네트워크/CPU 낭비를 없앤 KEP-5866 Alpha 기능. |
| [Kubernetes SELinux Mount GA](oss-changes/kubernetes/1.36-selinux-mount-ga.md) | RWOP 볼륨에 한해 컨테이너 런타임의 재귀적 SELinux 재라벨링 대신 커널 마운트 옵션으로 라벨을 한 번에 적용하는 방식이 GA로 승격됐다. |
| [Kubernetes PodGroup API를 Workload에서 분리해 독립 런타임 객체로 만들기](oss-changes/kubernetes/1.36-kubernetes-decouple-podgroup-api-from-workload-api.md) | KEP-5832로 PodGroup을 Workload 내장에서 독립 API 객체로 분리하는 v1alpha2 설계 변경 분석 |
| [Scheduler Parallel PreBind Plugins](oss-changes/kubernetes/1.36-kubernetes-parallel-prebind-plugins.md) | kube-scheduler binding cycle의 PreBind 플러그인이 항상 순차 실행되어 볼륨 바인딩 대기와 DRA 디바이스 attach 대기가 합산되던 문제를, PreBindPreFlight이 AllowParallel을 함께 반환하고 연속된 병렬 허용 플러그인을 그룹으로 묶어 동시에 실행하도록 바꾼 v1.36 스케줄링 프레임워크 변경 분석 |

</details>

<details>
<summary>Karpenter (5)</summary>

| 변경 | 내용 |
| --- | --- |
| [Disruption Budgets (NodePool)](oss-changes/karpenter/1.0-disruption-budgets-nodepool.md) | v1.0에서 NodePool.Spec.Disruption.Budgets 도입 — cron 시간창 × 동시성 제한 교집합 방식으로 노드 제거 통제 |
| [v1.14 — Karpenter Balanced Consolidation](oss-changes/karpenter/1.14-balanced-consolidation-scoring.md) | 저장액 대비 disruption 비율을 점수화해 손해 보는 통합(consolidation)을 걸러내는 새 consolidationPolicy: Balanced 도입 |
| [Karpenter CapacityBuffer: 가상 파드로 여유 용량을 미리 만들어두는 사전 프로비저닝](oss-changes/karpenter/1.14-capacity-buffer-active-provisioning.md) | v1.14에서 alpha로 추가된 CapacityBuffer API가 파드 없이도 노드를 미리 켜두는 방식(가상 파드를 매 루프 주입)과, 그로 인한 노미네이션/emptiness/consolidation 경계 처리를 다룬다. |
| [Karpenter Dynamic Resource Allocation Scheduling](oss-changes/karpenter/1.14-dynamic-resource-allocation-scheduling.md) | Karpenter가 아직 인스턴스 타입이 확정되지 않은 NodeClaim 상태에서 GPU 등 DRA 디바이스를 배분하기 위해 전용 할당기(pkg/scheduling/dynamicresources)를 새로 구현하고 스케줄러/디스럽션/노드 초기화 전반에 통합한 변경을 분석. |
| [Karpenter Resize-Aware Resource Accounting](oss-changes/karpenter/1.14-karpenter-resize-aware-resource-accounting.md) | InPlacePodVerticalScaling 도중 spec.requests만 읽던 Ceiling() 계산을 kube-scheduler와 동일하게 status 기반 최댓값으로 고친 v1.14.0 변경 분석 |

</details>

<details>
<summary>Cilium (4)</summary>

| 변경 | 내용 |
| --- | --- |
| [로드밸런싱 컨트롤 플레인 재설계](oss-changes/cilium/1.18-loadbalancer-statedb-redesign.md) | v1.18에서 뮤텍스+해시맵 기반 명령형 모델을 StateDB 테이블 기반 데이터 중심 모델로 전환 |
| [로드밸런서 백엔드 평탄화 (Aggregated Load-Balancer State)](oss-changes/cilium/1.20-loadbalancer-backend-flatten.md) | v1.19에서 서비스별 인스턴스를 중첩 맵으로 담던 백엔드 행 구조가 실제 프로덕션 메모리 급증을 유발한 사례 — v1.20에서 (서비스,주소,우선순위) 조합마다 독립된 테이블 행으로 평탄화해 해결 |
| [Cilium Datapath Plugins](oss-changes/cilium/1.20-cilium-datapath-plugins.md) | 서드파티 BPF 프로그램을 Cilium의 datapath에 안전하게 끼워 넣을 수 있는 gRPC 기반 플러그인 프로토콜과 디스패처 프로그램 메커니즘 |
| [Cilium Gateway ListenerSet Delegation](oss-changes/cilium/1.20-cilium-gateway-listenerset-delegation.md) | 공유 Gateway의 listener 소유권을 ListenerSet으로 위임하고, Cilium이 병합된 Envoy 설정과 리소스별 TLS 권한 경계를 함께 유지 |

</details>

<details>
<summary>OPA (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [컨테이너 자원 인지 (GOMAXPROCS/GOMEMLIMIT)](oss-changes/opa/1.18.0-container-aware-gomaxprocs-gomemlimit.md) | v1.18.0에서 automaxprocs 복원 + automemlimit 신규 추가 — Go 네이티브 cgroup 인지의 최소값 차이(1 vs 2)로 저메모리 배포에서 발생한 OOM 회귀를 되돌린 사례 |

</details>

<details>
<summary>LoxiLB (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [LoxiLB HA Egress Cluster Routing](oss-changes/loxilb/0.9.8-ha-egress-cluster-routing.md) | v0.9.8에서 기존 LB·VIP·HA 상태 기계를 egress 모드로 연결해, 대기 노드의 트래픽을 전용 VXLAN으로 활성 노드에 전달하고 안정적인 SNAT/VIP를 유지하는 초기 설계. |

</details>

<details>
<summary>Ceph (3)</summary>

| 변경 | 내용 |
| --- | --- |
| [Ceph mgmt-gateway High Availability](oss-changes/ceph/20.2.0-mgmt-gateway-ha.md) | Tentacle에서 Dashboard와 monitoring endpoint를 NGINX 기반 단일 TLS 경계로 모으고, virtual IP·keepalived·stateless oauth2-proxy로 gateway 자체의 HA까지 보완한 설계. |
| [Ceph FastEC Partial Writes](oss-changes/ceph/20.2.0-fastec-partial-writes.md) | 소거 코드 풀의 덮어쓰기가 스트라이프 전체를 읽고 다시 쓰던 방식 대신, 건드린 조각만 처리하는 partial write와 parity delta write로 I/O 증폭을 줄이는 새 경로가 추가됐다. |
| [Ceph mClock EC SubOp Read 스케줄링 분류 개선](oss-changes/ceph/20.2.3-ceph-mclock-ec-subop-read-classification.md) | EC SubOp 읽기의 priority 기반 mClock 분류와 프로파일 조정으로 대규모 EC 클러스터의 백필 시 클라이언트 I/O 지연 개선 |

</details>

<details>
<summary>OVN (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [OVN Flow-Based Tunnels](oss-changes/ovn/26.03-flow-based-tunnels.md) | v26.03에서 원격 chassis별 tunnel port 대신 type별 shared port를 만들고 OpenFlow가 패킷마다 tunnel endpoint를 설정해 대규모 환경의 port 수를 줄인 실험적 설계. |

</details>

<details>
<summary>Slurm (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Slurm 외부 이종 작업 수명주기](oss-changes/slurm/slurm-26-05-3-1-slurm-external-heterogeneous-job-lifecycle.md) | 외부 컴포넌트와 배치 리더의 책임을 분리 |

</details>

<details>
<summary>containerd (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [containerd CRI Checkpoint Restore 경로 분리](oss-changes/containerd/2.3.4-containerd-cri-checkpoint-restore-path-separation.md) | Checkpoint 생성과 CreateContainer 복원 권한을 분리 |

</details>

<details>
<summary>Istio (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>etcd (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Cluster API (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Harvester (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Envoy (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Envoy CPU-local Connection Steering](oss-changes/envoy/1.39-envoy-cpu-local-connection-steering.md) | accept마다 mutex를 잡는 정확한 균형 대신 SO_REUSEPORT BPF가 수신 CPU에 고정된 worker socket으로 연결을 보내는 Linux TCP 설계 |

</details>

<details>
<summary>libvirt (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>ClickHouse (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>ScyllaDB (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>Apache Kafka (1)</summary>

| 변경 | 내용 |
| --- | --- |
| [Kafka 컨트롤러 등록 해제](oss-changes/kafka/2026-08-03-kafka-controller-unregistration.md) | KRaft 컨트롤러 등록을 지울 방법이 없어 이미 클러스터에서 빠진 노드의 등록이 피처 업그레이드를 계속 거부하던 문제를, UnregisterController RPC와 UnregisterControllerRecord로 메타데이터 계층에 등록 해제 경로를 뚫어 해결한 KIP-1312 변경 분석 |

</details>

<details>
<summary>TCMalloc (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>

<details>
<summary>RabbitMQ (0)</summary>

| 변경 | 내용 |
| --- | --- |

</details>
