# xfx

## 마지막 업데이트 (2026-07-10)

| CVE | 도메인 | 내용 |
| --- | --- | --- |
| [CVE-2025-21333](vulnerability/windows/CVE-2025-21333-windows-hyperv-crossvmevent-heap-overflow.md) | Windows | Hyper-V NT Kernel Integration VSP의 CrossVmEvent 생성 경로에서 발생한 heap overflow가 WNF/I/O ring 기반 커널 객체 손상과 로컬 권한 상승으로 이어질 수 있는 취약점이다. |

<details>
<summary>Linux (18)</summary>

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

</details>

<details>
<summary>Opensource (4)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2008-0166](vulnerability/opensource/CVE-2008-0166-debian-openssl-predictable-prng.md) | Debian OpenSSL 패치 실수로 PRNG 엔트로피가 PID 값 하나로 축소된 이슈 |
| [CVE-2019-5736](vulnerability/opensource/CVE-2019-5736-runc-proc-self-exe-escape.md) | runc가 `/proc/self/exe`를 통해 호스트 바이너리를 덮어쓸 수 있는 컨테이너 탈출 |
| [CVE-2026-0770](vulnerability/opensource/CVE-2026-0770-langflow-validate-code-exec-rce.md) | Langflow의 코드 "검증" 엔드포인트가 실제로는 `exec()`로 임의 코드를 실행하던 취약점 |
| [CVE-2019-11358](vulnerability/opensource/CVE-2019-11358-jquery-extend-prototype-pollution.md) | jQuery의 $.extend(true, ...) 깊은 병합 로직이 속성 이름 __proto__를 일반 키와 구분하지 않아, 공격자가 넣은 데이터가 재귀적으로 전역 공유 객체인 Object.prototype 자체를 오염시킬 수 있었다. |

</details>

<details>
<summary>Windows (3)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2023-23397](vulnerability/windows/CVE-2023-23397-outlook-ntlm-reminder.md) | Outlook reminder 처리 중 사용자 상호작용 없이 NTLM 해시가 유출되는 권한 상승 |
| [CVE-2025-33073](vulnerability/windows/CVE-2025-33073-windows-smb-client-ntlm-reflection.md) | Windows SMB Client가 marshalled target info가 붙은 특수 대상 이름을 로컬 호스트로 오판해, SMB signing이 강제되지 않은 환경에서 NTLM reflection을 SYSTEM 권한 작업으로 확장할 수 있는 취약점이다. |
| [CVE-2025-21333](vulnerability/windows/CVE-2025-21333-windows-hyperv-crossvmevent-heap-overflow.md) | Hyper-V NT Kernel Integration VSP의 CrossVmEvent 생성 경로에서 발생한 heap overflow가 WNF/I/O ring 기반 커널 객체 손상과 로컬 권한 상승으로 이어질 수 있는 취약점이다. |

</details>

<details>
<summary>Browser (3)</summary>

| CVE | 내용 |
| --- | --- |
| [CVE-2026-56645](vulnerability/browser/CVE-2026-56645-edge-heap-buffer-overflow.md) | Microsoft Edge(Chromium 기반) 힙 버퍼 오버플로우 |
| [CVE-2026-57572](vulnerability/browser/CVE-2026-57572-crawl4ai-chromium-argument-injection.md) | Crawl4AI가 요청 값을 Chromium 실행 인자로 그대로 넘겨 발생한 원격 코드 실행 |
| [CVE-2026-58289](vulnerability/browser/CVE-2026-58289-edge-type-confusion.md) | Microsoft Edge(Chromium 기반) type confusion |

</details>
