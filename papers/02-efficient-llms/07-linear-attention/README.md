# 07. Linear Attention

**Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention** (2020)

[원문·서지 정보](https://arxiv.org/abs/2006.16236) · [PDF](https://arxiv.org/pdf/2006.16236) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 추가
- **한 줄 설명:** Kernel feature map과 누적 상태로 선형 어텐션을 표현하는 기초.
- **읽을 부분:** Linear Transformer 수식과 causal/recurrent 표현. 고정된 feature dimension에서 시퀀스 길이에 따른 비용을 봅니다.
- **완료 기준:** 과거 토큰을 매번 읽는 방식과 상태 S·정규화 상태 Z를 갱신하는 방식을 비교한다.
- **구분할 점:** 일반 softmax attention을 아무 변화 없이 정확한 고정 크기 RNN으로 바꾸는 논문이라고 해석하면 안 됩니다. kernel feature map을 사용하는 attention을 다룹니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [용진테크 — Transformers are RNNs](https://jinyongyun.tistory.com/7) | 선형 어텐션 수식과 RNN 해석을 짧게 복습. | 본문 |
| 한국어 · AI 요약 | [Liner — Transformers are RNNs 퀵 리뷰](https://liner.com/ko/review/transformers-are-rnns-fast-autoregressive-transformers-with-linear-attention) | 연구 질문과 결과를 빠르게 훑는 보조 자료. 수식·수치는 원문과 대조. | 본문 |
| 영어 · 수학 해설 | [Data Processing Club — A Kernel Perspective](https://data-processing.club/transformers-are-rnns/) | kernel 관점에서 Transformer와 recurrent 표현의 관계를 설명. | 본문 |
| 영어 · 영상 안내 | [Yannic Kilcher — Transformers Are RNNs](https://www.classcentral.com/course/youtube-transformers-are-rnns-fast-autoregressive-transformers-with-linear-attention-paper-explained-126723) | kernel·선형화·RNN 연결을 다루는 영상 목차. 강의 안내 페이지. | 본문 |


## 학습 기록

- [5주차 계획](../../../curriculum/week-05/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
