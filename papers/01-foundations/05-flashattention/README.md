# 05. FlashAttention

**FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness** (2022)

[원문·서지 정보](https://arxiv.org/abs/2205.14135) · [PDF](https://arxiv.org/pdf/2205.14135) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Attention 수식의 의미를 유지하며 tiling과 online softmax로 GPU 메모리 이동을 줄임.
- **읽을 부분:** HBM/SRAM 계층, tiling, online softmax, backward 재계산. 첫 독해에서는 CUDA 세부 구현을 생략해도 됩니다.
- **완료 기준:** N×N attention 행렬을 HBM에 저장하지 않고 결과를 누적하는 방법을 설명한다.
- **구분할 점:** 기본 dense FlashAttention의 연산량은 여전히 시퀀스 길이에 대해 제곱입니다. exact는 수학적 알고리즘의 의미이며 부동소수점 비트 단위 동일성을 뜻하지 않습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 개념 해설 | [Zero Math AI — FlashAttention](https://zeromathai.com/flashattention/) | 메모리 병목과 online softmax의 직관을 먼저 익히기. | 본문 |
| 한국어 · 기술 노트 | [영삼넷 — 플래시 어텐션](https://www.youngsam.net/notes/flash-attention-io-aware-gpu-memory) | HBM/SRAM 이동과 중간 행렬 저장의 차이를 복습. | 본문 |
| 영어 · 저자 블로그 | [Tri Dao — Fast Transformer Training with Long Sequences](https://hazyresearch.stanford.edu/blog/2023-01-12-flashattention-long-sequences) | FlashAttention의 동기와 긴 시퀀스에서의 구현 개선. | 본문 |
| 영어 · 후속 연구 해설 | [Tri Dao / Together AI — FlashAttention-2](https://www.together.ai/blog/tri-dao-flash-attention) | 병렬화와 work partitioning으로 확장. 원 논문 이후의 심화 자료. | 본문 |


## 학습 기록

- [3주차 계획](../../../curriculum/week-03/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
