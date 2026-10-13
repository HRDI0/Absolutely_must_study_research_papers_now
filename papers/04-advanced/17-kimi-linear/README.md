# 17. Kimi Linear

**Kimi Linear: An Expressive, Efficient Attention Architecture** (2025)

[원문·서지 정보](https://arxiv.org/abs/2510.26692) · [PDF](https://arxiv.org/pdf/2510.26692) · [논문 목록](../../README.md)

- **우선순위:** 목적별 심화 · 필수→심화
- **한 줄 설명:** Gated DeltaNet을 발전시킨 KDA와 MLA를 결합한 하이브리드 비교 사례.
- **읽을 부분:** KDA의 channel-wise gating, KDA/MLA layer mix, state와 KV cache. 8번 이후 또는 10번과 비교.
- **완료 기준:** GDN의 gate와 KDA의 gate가 제어하는 단위를 비교한다.
- **구분할 점:** Linear라는 이름에도 전체 모델은 MLA layer를 포함한 하이브리드입니다. 전체 모델의 KV cache가 완전히 사라진다고 읽지 마세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [secrett2633 — Kimi Linear](https://blog.secrett2633.cloud/ai/review/2025-10-31-Kimi-Linear-An-Expressive-Efficient-Attention-Architecture) | KDA·하이브리드 구조·효율 결과의 개요. | 본문 |
| 한국어 · AI 요약 | [Moonlight — Kimi Linear](https://www.themoonlight.io/ko/review/kimi-linear-an-expressive-efficient-attention-architecture) | 설계 요소를 한국어로 다시 훑는 보조 자료. | 본문 |
| 영어 · 공식 구조·구현 개요 | [MoonshotAI — Kimi Linear](https://github.com/MoonshotAI/Kimi-Linear) | KDA와 MLA 비율, 모델 개요, 구현 자료 확인. | 본문 |
| 영어 · 비교 해설 | [Sebastian Raschka — Hybrid Attention](https://sebastianraschka.com/llm-architecture-gallery/hybrid-attention/) | Gated DeltaNet과 KDA를 하이브리드 패턴 안에서 비교. | 본문 |


## 학습 기록

- [14주차 계획](../../../curriculum/week-14/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
