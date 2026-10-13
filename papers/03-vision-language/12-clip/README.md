# 12. CLIP

**Learning Transferable Visual Models From Natural Language Supervision** (2021)

[원문·서지 정보](https://arxiv.org/abs/2103.00020) · [PDF](https://arxiv.org/pdf/2103.00020) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 이미지·텍스트 dual encoder를 대조학습하여 서로 대응하는 표현을 학습.
- **읽을 부분:** Image/text encoder, similarity matrix, contrastive loss, zero-shot classification.
- **완료 기준:** 학습의 정답 이미지–문장 쌍과 추론의 label prompt가 어떻게 다른지 설명한다.
- **구분할 점:** CLIP은 문장을 생성하는 대화 모델이 아닙니다. 이미지·텍스트 표현 정렬과 LLM에 visual token을 주입하는 작업은 구분해야 합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [yuha933 — CLIP 논문 리뷰](https://yuha933.tistory.com/22) | 대조학습과 zero-shot transfer의 기본 흐름. | 본문 |
| 한국어 · AI 작성 해설 | [Akileo — 이미지와 텍스트를 잇는 CLIP의 발상](https://akileox.github.io/ai/learning-transferable-visual-models-from-natural-language-supervision/) | 직관적인 재설명. 페이지에 AI 분석·초안 작성이 명시된 보조 자료. | 본문 |
| 영어 · 공식 블로그 | [OpenAI — CLIP: Connecting text and images](https://openai.com/index/clip/) | 연구 동기·접근·한계를 함께 읽는 첫 영어 자료. | 본문 |
| 영어 · 영상 안내 | [Yannic Kilcher — OpenAI CLIP](https://www.classcentral.com/course/youtube-openai-clip-connectingtext-and-images-paper-explained-126691) | 대조 목적함수·encoder 선택·zero-shot을 다루는 영상 소개와 목차. | 안내 페이지 |


## 학습 기록

- [9주차 계획](../../../curriculum/week-09/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
