# Prototype Pollution (프로토타입 오염)

**정의**: JavaScript의 객체들은 대부분 공통 조상인 `Object.prototype`을 공유한다. 사용자 입력을 검증 없이 객체에 재귀적으로 병합(deep merge)하는 코드가 `__proto__` 같은 특수 키를 그대로 처리해버리면, 공격자가 이 공유 프로토타입 자체에 속성을 주입할 수 있다.

**왜 문제가 되는가**: `Object.prototype`을 오염시키면, 그 뒤로 생성되는 **모든** 일반 객체가 그 오염된 속성을 상속받는다. 애플리케이션 로직이 "이 속성이 없으면 기본값"이라고 가정한 곳에서 예상치 못한 값이 나타나 인증 우회, 설정 조작, 심하면 코드 실행으로 이어질 수 있다.

**간단한 예시**:
```js
function merge(target, src) {
  for (const key in src) target[key] = src[key];   // __proto__ 도 그대로 처리
}
merge({}, JSON.parse('{"__proto__": {"isAdmin": true}}'));
({}).isAdmin;   // → true, 완전히 새로운 빈 객체도 오염된 속성을 상속받음
```

**이 저장소의 예**: [CVE-2019-11358](../vulnerability/opensource/CVE-2019-11358-jquery-extend-prototype-pollution.md) (jQuery `$.extend`)
