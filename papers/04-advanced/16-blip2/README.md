# 16. BLIP-2

**BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models** (2023)

[원문·서지 정보](https://arxiv.org/abs/2301.12597) · [PDF](https://arxiv.org/pdf/2301.12597) · [논문 목록](../../README.md)

- **우선순위:** 목적별 심화 · 심화 추가
- **한 줄 설명:** 동결된 vision encoder와 LLM 사이를 Q-Former로 연결하는 대안.
- **읽을 부분:** Q-Former의 learnable query, cross-attention, 두 단계 사전학습. LLaVA 다음 비교용.
- **완료 기준:** Projector와 Q-Former가 시각 정보를 LLM에 전달하는 방식의 차이를 설명한다.
- **구분할 점:** 모든 현대 VLM이 Q-Former를 쓰는 것은 아닙니다. Vision–Language 연결 구조의 설계 공간을 넓히기 위한 선택 자료입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 논문 리뷰 | [Luna AI — BLIP-2](https://lunaleee.github.io/posts/blip-2/) | Q-Former와 representation/generative learning 단계를 설명. | 본문 |
| 한국어 · 구조 요약 | [BLIP2, Q-Former 요약 정리](https://grow-up-by-coding.tistory.com/entry/Q-Former-란-요약-정리) | Q-Former 구조를 짧게 복습하는 보조 자료. | 본문 |
| 영어 · 저자 참여 설명·실습 | [Hugging Face — Zero-shot image-to-text generation with BLIP-2](https://huggingface.co/blog/blip-2) | 구조 소개와 captioning·VQA 실습을 연결. | 본문 |
| 영어 · 공식 연구 블로그 | [Salesforce — BLIP-2](https://www.salesforce.com/blog/blip-2/) | 연구팀의 사전학습 전략과 모듈 연결 설명. | 본문 |


## 학습 기록

- [13주차 계획](../../../curriculum/week-13/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
