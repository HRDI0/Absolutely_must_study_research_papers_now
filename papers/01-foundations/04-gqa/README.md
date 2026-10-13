# 04. GQA

**GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints** (2023)

[원문·서지 정보](https://arxiv.org/abs/2305.13245) · [PDF](https://arxiv.org/pdf/2305.13245) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 여러 Query head가 K/V head를 공유해 KV cache와 메모리 읽기 부담을 줄임.
- **읽을 부분:** MHA→MQA/GQA 체크포인트 변환, head pooling, uptraining, 그룹 수에 따른 절충.
- **완료 기준:** Query head 32개, KV head 8개인 예에서 MHA 대비 KV cache 비율을 계산한다.
- **구분할 점:** 같은 layer 수·head dimension·dtype·문맥 길이를 가정하면 KV head 수 감소에 비례해 캐시가 줄어듭니다. 전체 모델 메모리가 같은 비율로 줄어드는 것은 아닙니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — GQA](https://kimjy99.github.io/논문리뷰/gqa/) | head 공유와 체크포인트 변환을 논문 흐름대로 설명. | 본문 |
| 한국어 · 개념 해설 | [IBM — 그룹화된 쿼리 어텐션](https://www.ibm.com/kr-ko/think/topics/grouped-query-attention) | MHA·MQA·GQA를 처음 비교할 때 읽기. | 본문 |
| 영어 · 개념 해설 | [Thinam Tamang — Grouped Query Attention](https://thinamxx.github.io/blog/posts/GQA/gqa.html) | 짧은 GQA 개념 설명과 구현 방향. | 본문 |
| 영어 · 수식·구현 해설 | [Pratham Patel — Grouped-Query Attention](https://prathamp.com/blog/attention-gqa/) | 캐시 크기와 head 그룹 구성을 더 자세히 공부. | 본문 |


## 학습 기록

- [3주차 계획](../../../curriculum/week-03/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
