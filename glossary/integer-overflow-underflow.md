# Integer Overflow / Underflow (정수 오버플로우/언더플로우)

**정의**: 정수 연산 결과가 그 타입이 표현할 수 있는 범위를 벗어나면, 값이 반대편으로 "돌아서" 전혀 다른(대개 훨씬 작거나 훨씬 큰) 값이 되어버린다. 최댓값을 넘기면 오버플로우(다시 작은 값으로), 0 밑으로 내려가면 언더플로우(다시 큰 값으로, 특히 unsigned 타입에서)라고 부른다.

**왜 문제가 되는가**: 이 값이 버퍼 크기나 배열 인덱스, 반복 횟수 계산에 쓰이면, "충분히 크다"고 검사했던 값이 실제로는 아주 작은(혹은 아주 큰) 값으로 뒤바뀌어 있어서 검사 자체가 무력화된다.

**간단한 예시**:
```c
uint8_t len = 250;
len += 10;              // 260은 uint8_t(0~255) 범위를 넘어서 4로 wrap
buffer[len] = 'x';       // 의도한 것보다 훨씬 작은 인덱스에 쓰기
```

**이 저장소의 예**: [CVE-2002-0639](../vulnerability/linux/CVE-2002-0639-openssh-challenge-response-integer-overflow.md) (OpenSSH keyboard-interactive), [CVE-2023-0179](../vulnerability/linux/CVE-2023-0179-nft-payload-vlan-overflow.md) (nft_payload 정수 언더플로우)
