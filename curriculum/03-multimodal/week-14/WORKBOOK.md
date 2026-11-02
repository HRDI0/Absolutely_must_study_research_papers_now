# 14주 실습 워크북 — 작은 학습으로 모델이 정보를 사용하는지 검증

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 네 개 실험을 분리

| 실험 | 출발 상태 | trainable | 먼저 통과할 것 |
|---|---|---|---|
| A. text LM | 2주 dense baseline | text 전체 | 한 문장 학습·생성·저장 |
| B. 이미지 색 | 내 작은 vision+connector+LM | 우선 전체 또는 명시한 단계 | 10주 gradient·mask |
| C. 영상 순서 | B의 입력 경로 | 영상/언어 관련 경로 | frame 순서·position |
| D. indexer | 검증된 작은 dense teacher | warm-up에는 indexer만 | 고정 분포 KL 예제 |

B의 랜덤 vision을 고정한 connector-only 실험은 경로 검사와 별도 대조군입니다. 원본 VLM의 pretrained alignment recipe와 동일하다고 기록하지 않습니다.

## 2. 이미지 데이터와 label

합성 vocab256에서는 BOS=240,EOS=241,PAD=242,IMG=243,VID=244,VS=245,VE=246을 사용합니다. Q=5,ANS=6,RED=7,BLUE=8로 둡니다. 기본 vocab16 실습과 섞지 않습니다.

64×64 이미지 한 장의 merger token이 4개라면 sequence는 [BOS,Q,VS,IMG,IMG,IMG,IMG,VE,ANS,RED,EOS]입니다. raw label은 RED/EOS만 남기고 나머지는 ignore로 둡니다. 다음 token으로 shift하면 ANS 위치 logits가 RED를 예측합니다.

첫 손계산은 10주차 표를 사용합니다. 모든 logits가 0이고 vocab256이면 유효 정답 token의 CE는 ln256≈5.545177입니다. PAD나 IMG 위치를 평균 분모에 넣지 않습니다.

## 3. 데이터 분리와 평가

색마다 같은 수의 이미지를 만들되 밝기/작은 노이즈/위치를 변형하고 생성 seed를 저장합니다. train과 validation에서 **같은 생성 결과가 재사용되지 않도록** 합니다. 작은 데이터에서 우연한 결과를 과장하지 않습니다.

| 대조 조건 | 확인할 질문 |
|---|---|
| 정상 이미지 | 질문이 같아도 이미지에 따라 답이 바뀌는가 |
| image-shuffle | 시각 정답 대응을 깨면 성능이 나빠지는가 |
| text-only | 질문/순서만으로 답을 외울 수 있는가 |
| 영상 frame reversal | 동일 frame 집합에서 순서 질문의 답이 달라지는가 |

같은 학습 step·seed·데이터 수 조건을 기록합니다. 성공 threshold는 실행 전에 정하고, 그 기준을 못 맞춘 실험도 결과에 남깁니다. 색 과제는 기능 검사이며 일반 이미지 대화 능력의 증거가 아닙니다.

## 4. 학습 시작과 실패 대응

작은 고정 배치부터 시작합니다. 매 step 유효 label 수·loss·gradient norm·NaN 여부를 기록합니다. 처음부터 영상과 QSA 학습을 동시에 켜지 않습니다.

loss가 줄지 않으면 label shift→gradient 연결→optimizer 대상→입력 값 범위 순서로 확인합니다. 잘못된 정답 주입이나 영상 순서 누수가 없는지 확인한 후 학습률을 조정합니다. 학습률·step 수는 실측해 정하며 성공값을 미리 보장하지 않습니다.

## 5. Indexer는 별도 워크시트

[INDEXER_TRAINING](INDEXER_TRAINING.md)의 고정 KL 분포부터 시작합니다. teacher 확보, complete-block mask, warm-up과 sparse 학습을 분리합니다. 이 실험이 미완료여도 이미지 실험의 성공 기록을 지우지 않습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
