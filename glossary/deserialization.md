# Deserialization (역직렬화) 취약점

**정의**: 직렬화된 데이터(바이트/문자열)를 다시 객체로 복원(역직렬화)하는 과정에서, 그 데이터가 신뢰할 수 없는 소스(사용자 입력, 캐시 등)에서 왔는데도 검증 없이 복원해버리는 취약점이다. 복원 과정 자체가 생성자 호출, 타입 인스턴스화 등 부수효과를 일으킬 수 있다는 게 핵심이다.

**왜 문제가 되는가**: 많은 언어의 역직렬화 메커니즘(Java `ObjectInputStream`, Python `pickle`, Ruby `Marshal` 등)은 "이 바이트열이 어떤 클래스의 객체다"라고 하면 그 클래스를 그대로 인스턴스화한다. 공격자가 악의적인 클래스/속성 조합을 직렬화 형태로 만들어 넣으면, 역직렬화되는 순간 임의 코드 실행으로 이어질 수 있다.

**간단한 예시**:
```python
import pickle
data = request.get_data()
obj = pickle.loads(data)   # data가 공격자 제어라면, 로드 자체가 임의 코드를 실행할 수 있음
```

**이 저장소의 예**: [CVE-2020-8165](../vulnerability/opensource/CVE-2020-8165-rails-cache-raw-marshal-load-rce.md) (Rails Marshal.load), [CVE-2026-33264](../vulnerability/opensource/CVE-2026-33264-airflow-trigger-deserialize-rce.md) (Airflow)
