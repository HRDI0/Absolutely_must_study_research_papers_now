# 19. Gemma 4

**Gemma 4 Technical Report** (2026)

[원문·서지 정보](https://arxiv.org/abs/2607.02770) · [PDF](https://arxiv.org/pdf/2607.02770) · [논문 목록](../../README.md)

- **우선순위:** 목적별 심화 · 선택 유지
- **한 줄 설명:** Dense/MoE 계열과 특히 12B의 encoder-free 멀티모달 입력을 비교.
- **읽을 부분:** 12B 입력 경로와 다른 Gemma 4 변형의 차이. LLaVA·Qwen3-VL과 연결 방식 비교.
- **완료 기준:** 깊은 별도 encoder를 없앤 것과 patch/frame projection을 없앤 것이 왜 다른지 설명한다.
- **구분할 점:** Encoder-free 특징을 Gemma 4 전체에 일반화하지 마세요. 여기서 핵심 비교 대상은 12B입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 구조 해설 | [Data Lab — 이미지·오디오를 바로 이해하는 Gemma 4 12B](https://datastudywithmomo.tistory.com/109) | Encoder-free의 의미와 입력 모듈을 한국어로 읽기. | 본문 |
| 한국어 · 구조·실행 분석 | [공밀레 — Gemma 4 12B 완전 분석](https://gong-mil-le.tistory.com/729) | 입력 구조와 로컬 실행 관점을 함께 설명. 하드웨어·성능 수치는 공식 자료와 별도 대조. | 본문 |
| 영어 · 공식 기술 해설 | [Google Developers — Gemma 4 12B Developer Guide](https://developers.googleblog.com/gemma-4-12b-the-developer-guide/) | Vision embedder와 audio wave projection 설명을 우선 읽기. | 본문 |
| 영어 · 공식 개요 | [Google — Introducing Gemma 4 12B](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemma-4-12b/) | 새 입력 구조의 동기와 전체 모델 개요. | 본문 |


## 학습 기록

- [16주차 계획](../../../curriculum/week-16/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
