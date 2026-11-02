# 검수 지적 1~2 — 시작과 decoder 전환

[검수 요약](CURRICULUM_REVIEW.md) · [다음 지적](REVIEW_FINDINGS_02.md)

당시 문서의 행 번호를 기록한 보강 전 검수 근거입니다. [현재 조치](REVIEW_RESOLUTION.md)를 함께 봅니다.

### 1. 시작에 필요한 역량을 검사하고 부족하면 돌아갈 자료가 필요하다 — 높음

**근거:** [1주차](../curriculum/01-basics/week-01/README.md) 5행은 행렬곱·미분·PyTorch 기본 사용을 선수로 가정한다. [2주차](../curriculum/01-basics/week-02/README.md) 33~35행은 곧바로 decoder-only 학습·생성과 합성 문장 과적합을 요구한다.

**막히는 지점:** broadcasting, transpose/reshape, autograd, cross entropy, optimizer, train/eval, 저장/복원, 데이터와 정답의 정렬은 논문 리뷰 영상만으로 자동 습득되지 않는다. Python만 아는 독자에게는 1주차를 반복하라는 안내로 충분하지 않다.

**보완:** 새 필독 논문 대신 시작 전 진단 6개를 둔다. (1) attention 각 축 설명, (2) 서로 다른 shape의 broadcasting 예측, (3) gradient 초기화와 backward 설명, (4) logits와 정답으로 CE 구성, (5) 작은 배치 학습, (6) 저장/재로딩 결과 비교. 실패한 항목만 기초 튜토리얼로 되돌아가게 한다. 아래의 PyTorch 자료를 연결하면 된다.

### 2. Annotated Transformer에서 decoder-only로 넘어가는 연결 설명이 부족하다 — 높음

**근거:** [2주차](../curriculum/01-basics/week-02/README.md) 13행은 Harvard 함수를 읽으라고 하지만, 15행의 코드 독해 링크는 목표 모델의 MLP/RMSNorm으로 간다. 34행은 encoder와 cross-attention 제거를 한 단계로 처리한다.

**막히는 지점:** source/target이 있는 번역 모델을 다음 토큰 LM으로 바꿀 때 데이터 배치, mask, forward 인자, 정답 shift, loss, 생성 루프를 함께 바꿔야 한다. Harvard의 Generator는 log-softmax를 반환하며, SublayerConnection은 논문의 설명과 다른 pre-norm 순서를 사용한다고 명시한다. 이를 모르고 여러 예제의 코드를 섞으면 비교 기준이 달라진다. [Harvard 원문](https://nlp.seas.harvard.edu/annotated-transformer/)

**보완:** 원본 함수 → 남길 역할 → 바꿀 입력/출력 → 확인할 결과의 대응표를 넣는다. 최초 모델은 고정 길이·작은 문자 사전·dense FFN·일반 residual로 제한한다. 현대 norm과 gate는 학습되는 기본 LM을 얻은 뒤 하나씩 변경한다. GPT 구현 강의는 이 전환을 돕는 선택 자료로 연결한다.

