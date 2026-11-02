# 1주 실습 워크북 — Transformer를 텐서 연산으로 읽기

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 어디까지 만들까

이번에는 **한 head의 causal attention**까지만 만듭니다. embedding·여러 head·FFN을 한 번에 연결하지 않습니다. 기본 shape는 Q=[B,H,T,d], K=[B,H,T,d], V=[B,H,T,dv]이고 score의 마지막 두 축은 query/key입니다.

## 2. 고정 입력과 예상 결과

B=H=d=dv=1, T=3. Q와 K의 모든 값은 0, V의 token별 값은 [2,4,8]입니다. scale은 √d=1, dropout은 끕니다.

| query 위치 | 허용 key | attention 확률 | 출력 |
|---|---|---|---|
| 0 | {0} | [1,0,0] | 2 |
| 1 | {0,1} | [1/2,1/2,0] | 3 |
| 2 | {0,1,2} | [1/3,1/3,1/3] | 14/3≈4.666667 |

첫 예제는 균일 attention만 검사합니다. 두 번째 검사는 T=2,d=1, 두 번째 query=1, K=[0,ln3], V=[2,4]로 둡니다. 두 번째 행의 확률은 [1/4,3/4], 출력은 3.5입니다. 이를 통해 softmax 축과 비균일 가중합도 확인합니다.

## 3. 작은 단계별 진행

| 단계 | 내가 작성할 것 | 넘어갈 조건 |
|---|---|---|
| A | QKᵀ와 scale | score shape [1,1,3,3] |
| B | causal mask | 위 표의 허용 key와 일치 |
| C | softmax와 V 가중합 | 두 고정 예제와 일치 |
| D | B=2,H=2의 head 분할/병합 | 샘플·head가 섞이지 않음 |

head 분할은 원래 [B,T,D]를 [B,T,H,d]로 나눈 뒤 축을 바꾼다는 뜻입니다. D=H×d인 기본 MHA에서 시작합니다.

## 4. 일부러 실패시켜 보기

- 미래 V=8을 800으로 바꾸면 query0/1 출력은 그대로여야 합니다.
- mask의 상삼각/하삼각을 뒤집으면 첫 query 출력이 바뀌어야 합니다.
- 두 번째 head만 바꿨을 때 병합 전 첫 head는 그대로여야 합니다.
- padding과 causal을 합칠 때 유효 key가 하나도 없는 행은 softmax에 그대로 넣지 않습니다. 우선 padding 없는 입력부터 통과시킵니다.

**힌트 순서:** 마지막 축이 key인가 → mask를 softmax 전에 적용했는가 → head를 병합하기 전에 축을 되돌렸는가.

## 5. 도움을 받을 자료

[Harvard](https://nlp.seas.harvard.edu/annotated-transformer/)의 `attention`과 `MultiHeadedAttention`만 읽습니다. 한국어 그림 설명은 README의 번역 링크를 사용합니다. GPT에는 위 숫자를 주고 “코드 없이 각 행의 계산을 검사해 달라”고 요청합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
