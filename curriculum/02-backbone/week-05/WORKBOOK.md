# 5주 실습 워크북 — Gated DeltaNet의 상태 갱신

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 상태가 저장하는 것

상태 S는 [dk,dv] 행렬입니다. key k가 어떤 value를 예측하는지 Sᵀk로 읽고, 새 value와의 오차만 씁니다.

감쇠 S̃=αS → 오차 e=v−S̃ᵀk → 새 상태 S'=S̃+βkeᵀ → 출력 y=S'ᵀq.

이 페이지의 q는 recurrence에 들어가는 **유효 query**입니다. 실제 모듈의 L2 normalization과 query scaling까지 포함한 q가 아닐 수 있으므로 모듈 대조 전에 그 전처리를 맞춥니다.

## 2. 벡터 손계산

S0은 2×2 영행렬, 두 step 모두 k=q=[1,0]입니다.

| step | α | β | v | 감쇠 상태의 첫 행 | 오차 e | 새 상태 첫 행 | y |
|---|---:|---:|---|---|---|---|---|
| 1 | 1 | 0.5 | [2,4] | [0,0] | [2,4] | [1,2] | [1,2] |
| 2 | 0.5 | 0.5 | [3,1] | [0.5,1] | [2.5,0] | [1.75,1] | [1.75,1] |

두 번째 행은 계속 [0,0]입니다. 같은 key가 나와도 이미 예측한 value를 뺀 뒤 업데이트하는 점을 설명합니다. α=1은 식을 확인하기 위한 경계 예제이며 실제 gate parameterization과 구분합니다.

## 3. 단계별 구현 과제

1. projection·conv 없이 위 recurrence만 구현하고 표와 비교합니다.
2. 한 시퀀스를 1+1 token으로 나누어 S1을 넘깁니다. 전체 실행의 y와 S2가 같아야 합니다.
3. batch와 head 축을 추가합니다. 한 head만 바꿨을 때 다른 head는 그대로인지 봅니다.
4. normalization·gate를 추가하기 전에 이 버전을 느린 정답 기준으로 보관합니다.

GDN state를 in-place로 갱신할 경우 저장해 둔 중간 상태까지 바뀌지 않는지 봅니다. 학습 graph에서의 detach와 inference state 저장은 별개입니다.

## 4. 오류를 구별하기

- 출력 폭이 dk로 나오면 transpose와 축을 확인합니다. 출력은 dv입니다.
- β=0이면 새 value는 쓰지 않지만 α에 의한 감쇠는 남습니다.
- α=0,β=0의 경계 검사에서는 state와 출력이 0이 됩니다.
- additive outer product 누적과 delta rule의 결과가 같은지 비교하지 말고, **같은 key가 두 번 나온 예제에서 차이가 생겨야 함**을 확인합니다.

## 5. 읽을 자료의 역할

논문은 recurrence와 block diagram, README의 한국어/영어 리뷰는 개념 복습에 사용합니다. [Raschka 페이지](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/)는 개념 개요이며 구현 튜토리얼은 아닙니다.

공식 파일에서는 class보다 먼저 `torch_recurrent_gated_delta_rule`의 token loop를 읽습니다. [순차 연산 부근](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L421). 각 줄을 위 네 식 중 어디에 해당하는지 표시합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
