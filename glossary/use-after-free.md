# Use-After-Free (UAF, 해제 후 사용)

**정의**: 메모리를 `free()`(또는 그에 준하는 해제 호출)로 반납한 뒤에도, 그 메모리를 가리키는 포인터(댕글링 포인터)를 통해 계속 읽거나 쓰는 버그다.

**왜 문제가 되는가**: 해제된 메모리는 곧 다른 목적으로 재할당될 수 있다. 공격자가 그 틈에 원하는 데이터로 그 자리를 채워두면, 원래 객체를 통해 접근하던 코드가 공격자가 심어둔 데이터를 "정상 객체"로 오인하고 사용하게 된다 — 여기서 임의 코드 실행까지 이어지는 경우가 많다.

**간단한 예시**:
```c
Widget *w = create_widget();
free(w);
// ... 다른 코드가 이 메모리를 재사용 ...
w->do_something();   // 이미 해제된(다른 용도로 재사용됐을 수 있는) 메모리를 그대로 사용
```

**이 저장소의 예**: [CVE-2023-32233](../vulnerability/linux/CVE-2023-32233-nf-tables-anon-set-uaf.md) (nf_tables 익명 집합), [CVE-2026-15113](../vulnerability/browser/CVE-2026-15113-chrome-android-autofill-uaf.md) (Chrome Autofill)
