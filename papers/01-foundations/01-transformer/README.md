# 01. Transformer

**Attention Is All You Need** (2017)

[원문·서지 정보](https://arxiv.org/abs/1706.03762) · [PDF](https://arxiv.org/pdf/1706.03762) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Q/K/V, Multi-Head Attention, FFN, residual, causal mask를 배우는 출발점.
- **읽을 부분:** §3 Model Architecture. encoder–decoder, self-attention, cross-attention을 구분합니다.
- **완료 기준:** Q, K, V와 attention score의 텐서 크기를 적고 causal mask가 필요한 이유를 설명한다.
- **구분할 점:** 원논문은 encoder–decoder입니다. 현대 decoder-only LLM과의 차이는 3번 LLaMA에서 연결합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 그림 해설 | [The Illustrated Transformer 번역](https://nlpinkorean.github.io/illustrated-transformer/) | 입문 첫 자료. 전체 구조에서 Q/K/V와 여러 head까지 그림으로 설명. | 본문 |
| 한국어 · 학습 노트 | [The Illustrated Transformer 학습 정리](https://ch010104.tistory.com/m/306) | 같은 개념을 한국어로 다시 복습하는 용도. 위 번역과 내용이 겹침. | 본문 |
| 영어 · 그림 해설 | [Jay Alammar — The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) | 한국어 번역의 원문. encoder와 decoder의 데이터 흐름을 설명. | 본문 |
| 영어 · 영상·시각화 | [3Blue1Brown — Attention in transformers](https://www.3blue1brown.com/lessons/attention/) | Q/K/V와 attention을 시각적으로 설명. 논문 전체 리뷰보다 개념 입문에 적합. | 본문 |
| 영어 · 구현 해설 | [Harvard NLP — The Annotated Transformer](https://nlp.seas.harvard.edu/annotated-transformer/) | PyTorch 코드와 논문을 함께 읽는 자료. 수집 도구의 페이지 크기 제한으로 본문 전체 확인은 제한됨. | 접근 제한 |


## 학습 기록

- [1주차 계획](../../../curriculum/week-01/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
