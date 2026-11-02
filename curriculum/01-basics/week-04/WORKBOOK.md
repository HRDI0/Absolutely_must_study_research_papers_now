# 4주 실습 워크북 — SwiGLU·MoE와 목표 모델 설정 읽기

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. Norm과 SwiGLU를 MoE보다 먼저

**RMSNorm 손계산:** x=[3,4], ε=0, scale=[1,1]인 연산 예제에서 RMS=√12.5, 출력≈[0.848528,1.131371]입니다. 실습의 실제 ε는 config 값으로 복원합니다. 평균을 빼는 LayerNorm과 다릅니다.

목표의 zero-centered norm parameter는 출력에 1+w를 곱하는 경우가 있습니다. w=0에서 출력을 0으로 만드는 일반 scale 해석과 섞지 않습니다. 모든 norm이 같은 parameterization인지는 class마다 확인합니다.

**SwiGLU:** u=Wgate·x, v=Wup·x, z=SiLU(u)⊙v, y=Wdown·z. u=0이면 z=0입니다. bias가 없는 output projection이라면 y도 0이어야 합니다.

## 2. Expert 가중합 검사

router 확률 p=[0.5,0.3,0.15,0.05], top-2. 선택 후 재정규화하면 [0.625,0.375]. expert 출력 e0=[2,0], e1=[0,4]의 합은 [1.25,1.5]입니다.

shared expert 출력=[2,2], shared gate logit=0으로 두면 sigmoid gate=0.5입니다. 최종 출력은 **[2.25,2.5]**입니다. 이 예제의 p는 softmax 이후 값이며 router 입력 logits와 구분합니다.

## 3. 모듈을 따로 완성하기

| 단계 | 산출물 | 검사 |
|---|---|---|
| A | RMSNorm/SwiGLU | 위 수치와 0 gate 사례 |
| B | dense FFN 1개 | input/output 폭 일치 |
| C | 4개 expert의 느린 반복 실행 | 수동 가중합과 일치 |
| D | shared expert 추가 | [2.25,2.5] 확인 |
| E | router 학습 경로 | 선택 weight와 expert parameter에 gradient |
| F | 기존 LM의 FFN 1개 교체 | dense baseline과 별도 loss 기록 |

top-k의 index 선택 자체는 미분 가능한 연산이 아닙니다. 선택된 routing weight와 expert 출력 경로의 gradient를 확인합니다. “안 뽑힌 expert에도 매번 gradient가 있어야 한다”는 조건을 두지 않습니다.

초기 router logit이 모두 같으면 tie가 생깁니다. 수치 검사는 먼저 동점 없는 위 입력으로 하고, 초기화·실제 학습에서 expert 사용 빈도를 별도로 기록합니다.

## 4. 막힐 때

가중합이 너무 작거나 크면 선택 후 재정규화와 shared gate를 먼저 봅니다. 모든 토큰이 한 expert로 가면 top-k axis와 router 초기화를 확인하고 usage histogram을 남깁니다. 8층 전체를 바꾸기 전에 한 FFN만 교체합니다.

**자료:** [공식 코드 지도](../../../target/CODE_MAP.md)의 MLP → TopKRouter → SparseMoeBlock 순서. 전체 DeepSeek 논문으로 범위를 늘릴 필요는 없습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
