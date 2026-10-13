# 02. RoFormer / RoPE

**RoFormer: Enhanced Transformer with Rotary Position Embedding** (2021)

[원문·서지 정보](https://arxiv.org/abs/2104.09864) · [PDF](https://arxiv.org/pdf/2104.09864) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Q·K의 회전으로 attention 내적에 상대 위치 차이를 반영하는 방법.
- **읽을 부분:** RoPE의 회전행렬과 상대 위치 유도. 2차원 쌍을 회전시키는 예부터 시작합니다.
- **완료 기준:** 회전된 Q와 K의 내적에 두 위치의 차이가 나타나는 이유를 설명한다.
- **구분할 점:** RoPE를 사용한다고 훈련 길이 밖으로 자동으로 정확히 일반화하는 것은 아닙니다. 길이 확장은 별도 설계 문제입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — RoFormer](https://kimjy99.github.io/논문리뷰/roformer/) | 수식과 위치 인코딩의 요구 조건을 따라 읽기. | 본문 |
| 한국어 · YouTube | [딥러닝 논문읽기 모임 — RoFormer](https://www.youtube.com/watch?v=tRe2XHF6UbQ) | 회전행렬을 이용하는 위치 임베딩 설명. 영상 전체 재생 검증은 하지 않음. | 영상 메타데이터 |
| 한국어 · 수식 정리 | [DONGJU — RoPE 논문정리](https://dongju923.github.io/paper/RoPE/) | 회전행렬과 상대 위치 관계를 재확인하기 위한 자료. | 본문 |
| 영어 · 단계별 유도 | [You could have designed state of the art positional encoding](https://huggingface.co/blog/designing-positional-encoding) | 위치 인코딩의 요구 조건에서 시작해 RoPE까지 유도. | 본문 |
| 영어 · 직관 설명 | [RoPE Explained With Clock Hands](https://projectcozy.me/blog/rotary-embeddings-explained) | 시곗바늘 비유로 회전과 여러 주파수의 역할을 이해. | 본문 |


## 학습 기록

- [2주차 계획](../../../curriculum/week-02/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
