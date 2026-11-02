# 6주 실습 워크북 — Qwen식 GDN과 Gated Residual

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 두 개의 과제로 나누기

먼저 **Qwen식 GDN 단일 모듈**, 다음으로 **GR read/write**를 검사합니다. 두 가지를 동시에 통합하면 gate·norm·branch 중 오류 위치를 찾기 어렵습니다. 식의 근거와 class는 [공식 코드 지도](../../../target/CODE_MAP.md)를 따릅니다.

## 2. GDN 앞뒤의 작은 연산

| 순서 | 할 일 | 독립 검사 |
|---|---|---|
| projection | q/k/v, decay/write/output gate의 폭 분리 | config로 각 shape 계산 |
| local conv | causal depthwise conv + activation | 미래 값 변경 시 과거 불변 |
| Q/K norm | head별 L2 norm | 영벡터와 일반 벡터 구분 |
| recurrence | 5주차 연산 | 같은 유효 q/k/v/gate 입력으로 비교 |
| output | norm·sigmoid gate·projection | gate logit0이면 gate=1/2 |

**conv 손계산:** 한 channel x=[1,2,3,4], 길이3 kernel=[1,1,1], 왼쪽 zero padding, activation 적용 전 결과는 [1,3,6,9]입니다. 실제 learned kernel과 여러 channel로 확장하기 전에 이 연산부터 확인합니다.

decay는 exp(−exp(A)·softplus(a+b))처럼 범위를 제한합니다. raw logit을 그대로 α로 쓰지 않습니다. target의 write/output gate, norm 정의를 각 class에서 따로 확인합니다.

## 3. GR의 읽기와 쓰기

이미 정규화된 4개 branch가 모두 [1,1]이라고 가정합니다. read gate가 모든 원소에서 1/2이면 branch 방향 평균으로 만든 sublayer 입력은 **[0.5,0.5]**입니다. 합산하면 [2,2]가 되므로 mean과 sum을 구분할 수 있습니다.

다음은 write만 떼어 낸 예제입니다. 원래 각 branch가 [1,1], sublayer 출력이 [2,−1], injection weight가 branch마다 1이면 새 branch는 모두 **[3,0]**입니다. 이 예제는 전체 GR의 학습된 norm/projection 출력을 가정한 단위 검사이며 실제 전체 모듈 출력의 보장값은 아닙니다.

## 4. 조립 순서

A. conv/gate/norm을 각각 검사 → B. GDN 1층 → C. GR read만 → D. GR write만 → E. attention 앞뒤 GR → F. MoE 앞뒤 GR.

모든 단계에서 input/output shape와 마지막 state를 남깁니다. layer 전체를 대조할 때는 최종 stream collapse도 포함합니다. [가중치 대응 절차](../../../target/PARITY.md)를 사용하고 같은 seed만으로 비교하지 않습니다.

## 5. 실패 진단

| 증상 | 먼저 볼 것 |
|---|---|
| 여러 branch로 바꾸자 출력이 4배 | read mean/sum |
| norm 뒤 모두 0 | zero-centered scale 해석 |
| decode 처음 몇 token만 다름 | causal conv의 왼쪽 padding/history |
| 원형 GDN과 끝까지 안 맞음 | sigmoid/SiLU gate·query scaling 차이 |
| GR만 비교할 때 불일치 | 비교 입력이 norm 전인지 후인지 |

GPT에게는 전체 class를 재작성시키지 말고 “이 중간 tensor가 어느 축으로 평균되어야 하는가”를 묻습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
