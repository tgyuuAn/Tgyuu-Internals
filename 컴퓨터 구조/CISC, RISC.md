### CISC

- **Complex** Instruction Set Computing
- 복잡한 명령어 집합을 사용하여 CPU가 다양한 연산을 직접 수행하는 방식
- 한 개의 명령어가 여러 개의 연산을 포함할 수 있음
- CPU 내부에서 하드웨어적으로 복잡한 연산을 처리할 수 있도록 설계됨
- 데스크톱, 서버 CPU에서 많이 사용됨 _(x86, Intel 등)_

```assembly
ADD [MEM1], [MEM2]  ; MEM1에 MEM2 값을 더하고, 결과를 MEM1에 저장
```

<br><br><br>

### RISC

- **Reduce** Instruction Set Computing
- 간단한 명령어 집합을 사용하여 CPU가 빠르게 실행되도록 설계된 방식
- 명령어가 단순하여 하나의 명령어가 하나의 연산만 수행함
- 명령어 길이가 고정적이며, 파이프라이닝 기술을 활용하여 더 빠른 실행 가능
- 모바일, IoT, 임베디드 기기와 같은 곳에서 많이 사용됨 _(대표적으로 ARM)_

```assembly
LDR R1, MEM1  ; MEM1 값을 R1 레지스터로 로드
LDR R2, MEM2  ; MEM2 값을 R2 레지스터로 로드
ADD R3, R1, R2  ; R1 + R2 결과를 R3에 저장
STR R3, MEM1  ; R3 값을 다시 MEM1에 저장
```