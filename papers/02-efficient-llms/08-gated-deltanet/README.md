# 08. Gated DeltaNet

**Gated Delta Networks: Improving Mamba2 with Delta Rule** (2024)

[원문·서지 정보](https://arxiv.org/abs/2412.06464) · [PDF](https://arxiv.org/pdf/2412.06464) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 기억을 감쇠하는 gate와 특정 연관 정보를 수정하는 delta rule을 결합.
- **읽을 부분:** Gate와 delta rule의 역할, state update, chunkwise 병렬 학습. 7번을 먼저 읽습니다.
- **완료 기준:** 단순 누적 메모리, gated decay, delta update의 차이를 설명한다.
- **구분할 점:** 최초 공개는 2024년이며 ICLR 2025 논문입니다. 전체 하이브리드 모델에는 attention layer도 남을 수 있습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어·영어 · 수식·논문 리뷰 | [Junwon Lee — Gated Delta Networks](https://romaanj.github.io/reviews/gated-deltanet/) | 언어 전환을 제공하는 상세 리뷰. fast-weight와 상태 갱신 관점. | 본문 |
| 한국어 · 연결 개념 해설 | [Taekhyun Park — Online Learning](https://thrillcrazyer.github.io/blog/2026/online-learning/) | 온라인 최적화 관점에서 delta rule과 gated delta rule을 연결. 수학 심화. | 본문 |
| 영어 · 구현 안내 | [Sebastian Raschka — Gated DeltaNet](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/) | 작은 구현을 통해 상태 갱신을 따라가는 보조 자료. | 본문 |
| 영어 · 공식 구현 | [NVlabs — GatedDeltaNet](https://github.com/NVlabs/GatedDeltaNet) | 공식 README·학습 설정·코드와 논문 수식을 연결. | 본문 |


## 학습 기록

- [6주차 계획](../../../curriculum/week-06/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
