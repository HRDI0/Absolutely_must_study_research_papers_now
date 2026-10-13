# 06. DeepSeek-V3

**DeepSeek-V3 Technical Report** (2024)

[원문·서지 정보](https://arxiv.org/abs/2412.19437) · [PDF](https://arxiv.org/pdf/2412.19437) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** MLA, sparse MoE, 부하 분산, Multi-Token Prediction을 결합한 LLM 구조.
- **읽을 부분:** Architecture의 MLA, DeepSeekMoE, auxiliary-loss-free load balancing, MTP. FP8·DualPipe는 시스템 심화.
- **완료 기준:** MLA의 캐시 압축과 MoE의 선택적 연산이 각각 무엇을 줄이는지 구분한다.
- **구분할 점:** MLA와 DeepSeekMoE는 V2에서 검증된 구조입니다. V3가 모두 처음 발명했다고 읽지 마세요. MoE 활성 파라미터 수는 전체 가중치 저장량과 다릅니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 논문 리뷰 | [EmjayAhn — DeepSeek-V3 Technical Report](https://emjayahn.github.io/2025/03/24/DeepSeek-V3-Technical-Report-정리/) | MLA·MoE·MTP와 학습 시스템을 폭넓게 정리. | 본문 |
| 한국어 · 아키텍처 리뷰 | [아이펠 리서치 — DeepSeek-V3: Efficiency at Scale](https://choiwonjin.tistory.com/88?category=1369230) | 주요 아키텍처인 MLA·DeepSeekMoE·MTP 중심. | 본문 |
| 영어 · 구조 비교 해설 | [Sebastian Raschka — From DeepSeek V3 to V3.2](https://magazine.sebastianraschka.com/p/technical-deepseek) | MLA와 MoE를 이해하고 후속 버전까지 비교. | 본문 |
| 영어 · 공식 구조·구현 개요 | [DeepSeek-AI — DeepSeek-V3 README](https://github.com/deepseek-ai/DeepSeek-V3/blob/main/README.md) | 공식 architecture 설명과 실행 자료. 튜토리얼보다 사실 확인용. | 본문 |


## 학습 기록

- [4주차 계획](../../../curriculum/week-04/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
