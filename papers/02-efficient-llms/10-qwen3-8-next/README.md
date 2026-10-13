# 10. Qwen3.8-Next

**On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability** (2026)

[원문·서지 정보](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Gated DeltaNet·QSA·Gated Residual·n-gram embedding을 통합해 성능·효율·안정성을 비교.
- **읽을 부분:** 네 가지 구조 변화와 ablation. pretraining loss, downstream 성능, prefill/decode 비용을 구분.
- **완료 기준:** GDN·QSA·MoE·외부 embedding이 각각 담당하는 역할을 설명한다.
- **구분할 점:** 논문 제목은 Qwen3.8-Next Architecture, 설명 대상 모델은 Qwen3.8-Flash-Next입니다. host memory의 n-gram embedding은 검색 기반 RAG와 다릅니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 구조 리뷰 | [dontech — Qwen3.8-Flash-Next 테크 리포트](https://donmain.dev/issue/qwen3-8-flash-next/) | 네 가지 아키텍처와 설계 실험을 정리. | 본문 |
| 한국어 · 개요 해설 | [Let's be Developers — Qwen3.8-Flash-Next](https://changsroad.tistory.com/611) | 구조 변경을 짧게 재확인. 정식 논문보다 먼저 나온 모델 발표 해설. | 본문 |
| 영어 · 공식 블로그 | [Alibaba Cloud — A New Architecture, Towards Ultimate Cost-Efficiency](https://www.alibabacloud.com/blog/qwen3-8-flash-next-a-new-architecture-towardsultimate-cost-efficiency_603501) | 팀의 모델 발표와 구성요소 개요. | 본문 |
| 영어 · 구조 해설 | [Research Papers — GDN, QSA, Gated Residual, N-gram](https://researchpapers.dev/en/explained/qwen3-8-next-architecture/) | 전체 구조를 짧게 복습하는 설명. 사실 판단은 원논문 우선. | 본문 |


## 학습 기록

- [7주차 계획](../../../curriculum/week-07/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
