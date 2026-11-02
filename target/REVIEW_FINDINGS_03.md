# 검수 지적 6~8 — 입력·학습·자료

[이전 지적](REVIEW_FINDINGS_02.md) · [검수 요약](CURRICULUM_REVIEW.md) · [현재 조치](REVIEW_RESOLUTION.md)

당시 문서의 행 번호를 기록한 보강 전 검수 근거입니다.

### 6. 멀티모달 입력 경로를 두 가지로 구분할 실행 안내가 필요하다 — 높음

**근거:** [구현 명세](IMPLEMENTATION.md) 29행의 vocab=256과 49행의 공식 tokenizer 혼용 금지, [11주차](../curriculum/03-multimodal/week-11/README.md) 33행의 공식 processor 출력 과제가 연결된다. [12주차](../curriculum/03-multimodal/week-12/README.md)는 작은 영상 크기를 직접 계산한다.

**막히는 지점:** 혼용하면 안 된다는 경고는 있지만, 실제로 어떤 경로를 먼저 사용할지 정하지 않았다. 공식 이미지 processor에는 크기 조정 설정도 있으므로 원본 이미지의 H/W로 손계산한 token 수가 실제 전처리 후와 같다고 가정할 수 없다. [고정 processor 설정](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/preprocessor_config.json)

**보완:** 첫 구현은 합성 사전·고정 해상도·정해진 frame 수로 시작하고, raw 크기와 전처리 후 크기를 모두 기록한다. 공식 processor를 보는 단계는 별도 입력 계약 비교로 둔다. 이미지 feature 수, placeholder 수, special ID, t/h/w 위치, PLE용 원래 ID를 표 한 장에 모은다. 단일 이미지 경로를 먼저 검증하고 여러 이미지·영상은 이후 추가한다. 축소 embedding에 공식 ID를 그대로 넣지 않는다.

### 7. 학습 가능한 연결과 실제 학습 실험 사이의 안내가 부족하다 — 높음

**근거:** [10주차](../curriculum/03-multimodal/week-10/README.md)는 connector freeze/unfreeze를 비교하고, [14주차](../curriculum/03-multimodal/week-14/README.md) 34~36행은 합성 VLM 과제, SFT, QSA indexer KL 학습을 함께 요구한다.

**막히는 지점:** 랜덤 초기화 또는 pretrained 시작 중 무엇인지, 어떤 모듈부터 학습하는지, teacher를 언제 확보하는지, loss의 합산·정규화·mask 범위가 충분히 정해져 있지 않다. 영상 과제와 indexer 학습이 동시에 실패하면 원인을 분리하기 어렵다. loss 감소와 gradient 존재만으로 입력을 올바르게 사용하는 모델이라고 결론낼 수도 없다.

**보완:** 작은 text-only 모델 학습 → 단일 이미지 합성 과제 → 영상 순서 과제 → indexer 학습으로 나눈다. 각 단계에 샘플·정답·loss mask 한 세트, 학습 가능한 parameter 목록, train/validation 구분, 대조군, 성공 판정 방법을 명시한다. KL은 고정 teacher 분포에서 먼저 손계산하고, 이후 실제 teacher와 연결한다. threshold는 실행을 통해 정해야 하며 임의의 성능 수치를 보장하지 않는다.

### 8. 보조 자료의 분류와 실제 깊이가 다르다 — 중간

**근거:** [자료실](../resources/README.md) 56행과 [5주차](../curriculum/02-backbone/week-05/README.md)는 Raschka의 GDN 페이지를 구현 안내로 소개한다. 재확인한 페이지는 recurrent memory와 hybrid의 개념을 짧게 소개하며, 단계별 구현 코드를 직접 전개하는 튜토리얼은 아니다. [실제 페이지](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/)

**보완:** '개념 개요'로 표시하고, 순차 갱신을 읽는 다음 자료를 별도로 연결한다. 이미 연결한 공식 modeling 파일에는 순차 gated delta rule 함수가 있으므로 모델 class 전체보다 이 작은 함수부터 읽게 하는 것이 낫다. [공식 순차 연산 부근](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L421)

영상도 '개념 설명', '수식 유도', '구현 실습'으로 구분하고 어느 막힘에 쓰는지 표시해야 한다. 영상을 더 많이 추가하는 것만으로는 충분하지 않다.

