# xfx

## 마지막 업데이트 (2026-07-12)

| CVE | 도메인 | 내용 |
| --- | --- | --- |
| [CVE-2002-0639](vulnerability/linux/CVE-2002-0639-openssh-challenge-response-integer-overflow.md) | Linux | OpenSSH의 keyboard-interactive 인증에서 클라이언트가 주장하는 응답 개수를 검증 없이 배열 크기 계산에 곱해, 정수 오버플로로 작게 할당된 힙 버퍼 너머로 원격 root 권한 쓰기가 가능했던 취약점. |
| [CVE-2018-16843](vulnerability/opensource/CVE-2018-16843-nginx-http2-frame-flood-memory-exhaustion.md) | Opensource | nginx HTTP/2가 SETTINGS/PING 확인 응답 프레임 할당 개수에 상한을 두지 않아, 클라이언트가 응답을 읽지 않으면서 프레임을 계속 보내면 워커 프로세스 메모리가 무제한으로 늘어날 수 있었던 취약점. |

<details>
<summary>Linux (21)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2014-6271](vulnerability/linux/CVE-2014-6271-shellshock.md) | Shellshock — Bash 함수 정의 파싱이 끝나지 않아 뒤에 붙은 명령까지 실행되는 취약점 |
| [CVE-2014-7169](vulnerability/linux/CVE-2014-7169-shellshock-incomplete-patch.md) | Shellshock 최초 패치가 불완전해 리다이렉션 경로로 우회된 후속 취약점 |
| [CVE-2018-15686](vulnerability/linux/CVE-2018-15686-systemd-notify-reexec-state-injection.md) | systemd 재실행 시 상태 역직렬화 스택 버퍼 오버플로우 |
| [CVE-2018-19788](vulnerability/linux/CVE-2018-19788-polkit-uid-int-overflow.md) | polkit이 UID를 부호 있는 정수로 다뤄 INT_MAX 초과 UID가 root로 오인되는 취약점 |
| [CVE-2019-13272](vulnerability/linux/CVE-2019-13272-ptrace-traceme-cred.md) | `ptrace_link()`가 잘못된 프로세스의 자격증명을 기록해 pkexec와 결합 시 권한 상승 |
| [CVE-2019-18276](vulnerability/linux/CVE-2019-18276-bash-disable-priv-mode.md) | Bash `disable_priv_mode()`가 saved-UID를 안 지워서 setuid 권한이 남는 취약점 |
| [CVE-2021-3560](vulnerability/linux/CVE-2021-3560-polkit-dbus-race.md) | polkit이 D-Bus 조회 실패를 root(UID 0)로 오인하는 레이스 컨디션 |
| [CVE-2021-4034](vulnerability/linux/CVE-2021-4034-pwnkit.md) | PwnKit — pkexec가 `argc=0` 실행을 예상 못해 환경변수를 인자로 오인 |
| [CVE-2022-0847](vulnerability/linux/CVE-2022-0847-dirty-pipe.md) | Dirty Pipe — 파이프 버퍼 flags 미초기화로 읽기 전용 파일 덮어쓰기 |
| [CVE-2022-1015](vulnerability/linux/CVE-2022-1015-nf-tables-register-overflow.md) | nf_tables 레지스터 번호 검증의 32비트 정수 오버플로우 |
| [CVE-2023-0179](vulnerability/linux/CVE-2023-0179-nft-payload-vlan-overflow.md) | nft_payload VLAN 헤더 처리의 정수 언더플로우로 인한 스택 버퍼 오버플로우 |
| [CVE-2023-32233](vulnerability/linux/CVE-2023-32233-nf-tables-anon-set-uaf.md) | nf_tables 배치 트랜잭션에서 익명 집합 비활성화 누락으로 인한 UAF |
| [CVE-2023-4147](vulnerability/linux/CVE-2023-4147-nftables-bound-chain-rule-injection-uaf.md) | 바인딩된 체인에 트랜잭션 로컬 ID로 규칙을 몰래 추가할 수 있는 검사 우회 |
| [CVE-2024-28085](vulnerability/linux/CVE-2024-28085-wall-escape-sequence-injection.md) | util-linux `wall`이 argv 경로만 이스케이프 필터링을 안 해 생긴 터미널 인젝션 |
| [CVE-2025-32463](vulnerability/linux/CVE-2025-32463-sudo-chroot-nsswitch.md) | sudo `--chroot`가 정책 검사보다 먼저 일어나 공격자의 nsswitch.conf를 신뢰하는 취약점 |
| [CVE-2025-6018](vulnerability/linux/CVE-2025-6018-pam-env-allow-active-spoof.md) | PAM `pam_env`로 SSH 세션을 물리 콘솔 세션처럼 속여 `allow_active` 권한 탈취 |
| [CVE-2026-28372](vulnerability/linux/CVE-2026-28372-telnetd-systemd-credentials-noauth-bypass.md) | util-linux 2.40의 systemd 자격증명 지원(login.noauth/CREDENTIALS_DIRECTORY)을 GNU inetutils telnetd가 클라이언트 환경변수를 무검증으로 전달하며 그대로 신뢰해버려, 로컬 사용자가 텔넷 접속만으로 root 인증을 건너뛸 수 있었다. |
| [CVE-2006-5051](vulnerability/linux/CVE-2006-5051-openssh-sigalrm-cleanup-double-free.md) | OpenSSH sshd의 로그인 유예시간 알람(SIGALRM) 핸들러가 인증 완료 여부를 구분하지 않고 비동기 시그널 불안전한 정리 함수(fatal/syslog, GSSAPI 정리)를 호출해 이중 해제로 이어질 수 있었던 경쟁 조건으로, 이 설계 결함은 18년 뒤 CVE-2024-6387로 재발했다. |
| [CVE-2018-16865](vulnerability/linux/CVE-2018-16865-systemd-journald-alloca-stack-clash.md) | systemd-journald의 네이티브 로그 프로토콜이 항목당 필드 개수에 상한을 두지 않아, journal_file_append_entry()가 필드 수에 비례한 크기(최대 약 4GB)를 검사 없이 alloca()로 할당하면서 스택이 인접 메모리 영역과 충돌(Stack Clash)해 DoS/코드 실행으로 이어질 수 있었던 취약점. |
| [CVE-2015-5602](vulnerability/linux/CVE-2015-5602-sudoedit-symlink-parent-dir-check-bypass.md) | sudoedit의 심볼릭 링크 방지 검사가 파일 바로 위 디렉터리 한 단계만 확인해, sudoers에 다중 와일드카드 경로를 쓰면 상위 디렉터리의 심볼릭 링크로 우회할 수 있었던 문제(1.8.15에서 최초 도입, 1.8.16에서 전체 경로 순회로 재작성) |
| [CVE-2002-0639](vulnerability/linux/CVE-2002-0639-openssh-challenge-response-integer-overflow.md) | OpenSSH의 keyboard-interactive 인증에서 클라이언트가 주장하는 응답 개수를 검증 없이 배열 크기 계산에 곱해, 정수 오버플로로 작게 할당된 힙 버퍼 너머로 원격 root 권한 쓰기가 가능했던 취약점. |

</details>

<details>
<summary>Opensource (7)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2008-0166](vulnerability/opensource/CVE-2008-0166-debian-openssl-predictable-prng.md) | Debian OpenSSL 패치 실수로 PRNG 엔트로피가 PID 값 하나로 축소된 이슈 |
| [CVE-2019-5736](vulnerability/opensource/CVE-2019-5736-runc-proc-self-exe-escape.md) | runc가 `/proc/self/exe`를 통해 호스트 바이너리를 덮어쓸 수 있는 컨테이너 탈출 |
| [CVE-2026-0770](vulnerability/opensource/CVE-2026-0770-langflow-validate-code-exec-rce.md) | Langflow의 코드 "검증" 엔드포인트가 실제로는 `exec()`로 임의 코드를 실행하던 취약점 |
| [CVE-2019-11358](vulnerability/opensource/CVE-2019-11358-jquery-extend-prototype-pollution.md) | jQuery의 $.extend(true, ...) 깊은 병합 로직이 속성 이름 __proto__를 일반 키와 구분하지 않아, 공격자가 넣은 데이터가 재귀적으로 전역 공유 객체인 Object.prototype 자체를 오염시킬 수 있었다. |
| [CVE-2020-8165](vulnerability/opensource/CVE-2020-8165-rails-cache-raw-marshal-load-rce.md) | Rails의 MemCacheStore/RedisCacheStore가 raw: true로 저장된(Marshal 직렬화를 거치지 않은) 캐시 값도 읽을 때 구분 없이 Marshal.load를 먼저 시도해, 공격자가 raw 캐시 값에 심은 Marshal 페이로드가 역직렬화되어 원격 코드 실행으로 이어질 수 있었던 취약점. |
| [CVE-2019-20372](vulnerability/opensource/CVE-2019-20372-nginx-error-page-request-smuggling.md) | nginx가 error_page를 외부 URL로 리다이렉트할 때 아직 읽지 않은 요청 바디를 폐기하지 않아, keep-alive 연결에서 그 바디가 다음 파이프라인 요청으로 오인되어 로드밸런서의 접근 제어를 우회하는 HTTP 요청 밀수가 가능했던 문제 |
| [CVE-2018-16843](vulnerability/opensource/CVE-2018-16843-nginx-http2-frame-flood-memory-exhaustion.md) | nginx HTTP/2가 SETTINGS/PING 확인 응답 프레임 할당 개수에 상한을 두지 않아, 클라이언트가 응답을 읽지 않으면서 프레임을 계속 보내면 워커 프로세스 메모리가 무제한으로 늘어날 수 있었던 취약점. |

</details>

<details>
<summary>Windows (4)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2023-23397](vulnerability/windows/CVE-2023-23397-outlook-ntlm-reminder.md) | Outlook reminder 처리 중 사용자 상호작용 없이 NTLM 해시가 유출되는 권한 상승 |
| [CVE-2025-33073](vulnerability/windows/CVE-2025-33073-windows-smb-client-ntlm-reflection.md) | Windows SMB Client가 marshalled target info가 붙은 특수 대상 이름을 로컬 호스트로 오판해, SMB signing이 강제되지 않은 환경에서 NTLM reflection을 SYSTEM 권한 작업으로 확장할 수 있는 취약점이다. |
| [CVE-2025-21333](vulnerability/windows/CVE-2025-21333-windows-hyperv-crossvmevent-heap-overflow.md) | Hyper-V NT Kernel Integration VSP의 CrossVmEvent 생성 경로에서 발생한 heap overflow가 WNF/I/O ring 기반 커널 객체 손상과 로컬 권한 상승으로 이어질 수 있는 취약점이다. |
| [CVE-2026-40369](vulnerability/windows/CVE-2026-40369-windows-kernel-pointer-overflow.md) | Windows Kernel이 신뢰할 수 없는 포인터와 길이 정보를 잘못 다룰 때 커널 풀 손상과 제한적 SYSTEM 권한 상승으로 이어질 수 있는 로컬 취약점이다. |

</details>

<details>
<summary>Browser (4)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2026-56645](vulnerability/browser/CVE-2026-56645-edge-heap-buffer-overflow.md) | Microsoft Edge(Chromium 기반) 힙 버퍼 오버플로우 |
| [CVE-2026-57572](vulnerability/browser/CVE-2026-57572-crawl4ai-chromium-argument-injection.md) | Crawl4AI가 요청 값을 Chromium 실행 인자로 그대로 넘겨 발생한 원격 코드 실행 |
| [CVE-2026-58289](vulnerability/browser/CVE-2026-58289-edge-type-confusion.md) | Microsoft Edge(Chromium 기반) type confusion |
| [CVE-2026-15113](vulnerability/browser/CVE-2026-15113-chrome-android-autofill-uaf.md) | Chrome Android Autofill의 renderer-browser form 상태와 객체 수명 관리가 어긋나 sandbox escape 가능성으로 이어질 수 있는 use-after-free 취약점이다. |

</details>
