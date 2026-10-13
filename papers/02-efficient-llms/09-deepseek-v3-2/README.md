# 09. DeepSeek-V3.2 / DSA

**DeepSeek-V3.2: Pushing the Frontier of Open Large Language Models** (2025)

[원문·서지 정보](https://arxiv.org/abs/2512.02556) · [PDF](https://arxiv.org/pdf/2512.02556) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Lightning Indexer와 token selection으로 중요한 과거 토큰에 sparse attention을 계산.
- **읽을 부분:** Architecture와 DSA. 구조 이해가 목표라면 후반 RL·agent 학습은 후순위.
- **완료 기준:** Indexer가 하는 일과 실제 attention이 하는 일을 분리해서 그린다.
- **구분할 점:** DSA가 KV cache를 없애는 것은 아닙니다. 선택 후 attention 비용뿐 아니라 indexer가 과거를 채점하는 비용도 고려해야 합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · AI 요약 | [Moonlight — DeepSeek-V3.2 리뷰](https://www.themoonlight.io/ko/review/deepseek-v32-pushing-the-frontier-of-open-large-language-models) | DSA·학습 단계를 한국어로 훑는 보조 요약. 원문 수식 대조 권장. | 본문 |
| 한국어 · AI 요약 | [Liner — DeepSeek-V3.2 퀵 리뷰](https://liner.com/ko/review/deepseekv32-pushing-frontier-open-large-language-models) | 연구 질문·방법·평가를 다른 요약으로 복습. | 본문 |
| 영어 · 상세 구조 해설 | [Sebastian Raschka — From DeepSeek V3 to V3.2](https://magazine.sebastianraschka.com/p/technical-deepseek) | MLA와 DSA의 변화, 구조와 RL 변화의 구분. | 본문 |
| 영어 · 구현 안내 | [DeepSeek Sparse Attention — From Scratch](https://sebastianraschka.com/llms-from-scratch/ch04/15_deepseek_sparse_attention/) | Lightning Indexer와 top-k 선택을 코드 관점으로 연결. | 본문 |


## 학습 기록

- [7주차 계획](../../../curriculum/week-07/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
