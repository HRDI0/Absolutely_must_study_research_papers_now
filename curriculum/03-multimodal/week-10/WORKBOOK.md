# 10주 실습 워크북 — LLaVA로 시각·언어 연결과 SFT 이해

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 목적과 출발 상태

10주차는 **단일 이미지의 정보가 decoder loss까지 연결되는지** 확인합니다. pretrained 시각/언어 능력이 생겼다고 주장하지 않습니다. vision encoder는 아직 학습되지 않은 작은 모델, text LM은 2주차의 작은 실험 상태를 사용한다고 명시합니다.

connector만 학습하는 실험은 gradient 경로 검사입니다. 랜덤 vision을 고정하고도 일반 이미지 설명이 될 것이라는 전제를 두지 않습니다. 실제 간단한 이미지 학습은 14주차에서 수행합니다.

## 2. 연결부 수치 예제

feature f=[1,2], 출력 폭3인 projection W의 행을 [1,0],[0,1],[1,1], bias=0으로 두면 Wf=[1,2,3]입니다. 한 token의 projector 검사를 먼저 통과시킨 뒤 여러 시각 token에 같은 변환을 적용합니다.

2×2 merge에서는 4개 patch feature를 정해진 순서로 묶습니다. 폭 Dv=64의 4개를 concat하면 256, projection 뒤 언어 폭128이 됩니다. concat 순서가 같아야 공식 merger와 비교할 수 있습니다.

## 3. Assistant-only loss의 위치

교육용 sequence는 [BOS,Q,IMG,ANS,RED,EOS]입니다. raw sequence 기준 mask는 [0,0,0,0,1,1]입니다.

| decoder 입력 | 다음 정답 | loss에 포함? |
|---|---|---|
| BOS | Q | 아니오 |
| Q | IMG | 아니오 |
| IMG | ANS | 아니오 |
| ANS | RED | 예 |
| RED | EOS | 예 |

IMG는 실제 visual embedding으로 치환할 위치입니다. 답변 직전 ANS 위치의 logits가 첫 정답 RED를 예측합니다. label mask를 먼저 만든 뒤 shift하거나, shift 뒤 만드는 방식 중 하나를 고정하고 표와 맞춥니다.

유효 정답이 두 개이면 loss 분모도 두 개입니다. 모든 label이 ignore인 샘플은 학습 전에 제외하거나 명시적으로 오류 처리합니다. 0으로 나누는 loss를 그대로 평균에 섞지 않습니다.

## 4. Freeze 실험

| 조건 | 예상 gradient |
|---|---|
| 모든 parameter 고정 | 학습 실험으로 사용하지 않음; forward만 확인 |
| connector만 학습 | connector gradient를 확인; vision/LM weight는 고정 |
| vision+connector 학습 | vision까지 전달되는지 확인 |
| 전체 학습 | 모든 활성 경로의 gradient를 검사 |

LM weight를 고정하는 것과 LM forward를 no_grad로 감싸는 것은 다릅니다. connector를 학습하려면 고정 LM을 통과해 입력 feature로 돌아가는 gradient는 필요합니다.

## 5. 오류와 힌트

이미지를 바꿔도 logits가 완전히 같으면 feature 치환 위치·detach·placeholder 개수를 확인합니다. loss는 변하지만 connector gradient가 없으면 no_grad 경계를 봅니다. 정답이 이상하게 잘 맞으면 RED가 예측 위치의 입력에 이미 들어갔는지 봅니다.

LLaVA의 연결/학습 원리를 읽되 목표형 merger는 [12주차](../week-12/README.md)에서 구현합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
