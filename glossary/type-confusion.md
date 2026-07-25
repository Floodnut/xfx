# Type Confusion (타입 컨퓨전)

**정의**: 메모리에 있는 어떤 객체를 실제 타입과 다른 타입으로 해석해서 사용하는 버그다. 특히 V8/JavaScriptCore 같은 JS 엔진처럼 동적 타입 언어를 구현하는 코드에서, 내부적으로 객체의 "모양(shape)"이 바뀌는데 엔진이 이를 놓치는 경우 자주 발생한다.

**왜 문제가 되는가**: 타입 A의 객체를 타입 B로 잘못 해석하면, B의 필드 오프셋 규칙으로 A의 메모리를 읽고 쓰게 된다. 이는 사실상 임의의 메모리 레이아웃을 공격자가 원하는 대로 재해석하게 해주는 것과 같아서, 메모리 손상으로 이어지기 쉽다.

**간단한 예시**:
```c
struct Small { int a; };
struct Big   { int a; void (*func_ptr)(); };

struct Small *s = alloc_small();
struct Big *b = (struct Big *)s;   // Small을 Big으로 잘못 캐스팅
b->func_ptr();                      // 실제로는 Small의 다음 메모리를 함수 포인터로 호출
```

**이 저장소의 예**: [CVE-2025-31277](../vulnerability/browser/CVE-2025-31277-javascriptcore-jit-type-confusion.md) (JavaScriptCore JIT), [CVE-2026-58289](../vulnerability/browser/CVE-2026-58289-edge-type-confusion.md) (Edge)
