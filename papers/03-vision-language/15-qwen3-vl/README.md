# 15. Qwen3-VL

**Qwen3-VL Technical Report** (2025)

[원문·서지 정보](https://arxiv.org/abs/2511.21631) · [PDF](https://arxiv.org/pdf/2511.21631) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** SigLIP-2 기반 encoder·merger·DeepStack·Interleaved MRoPE·영상 timestamp를 통합.
- **읽을 부분:** §2 Model Architecture와 §3.1 Training Recipe. 영상은 frame sampling·timestamp·토큰 예산도 함께 봅니다.
- **완료 기준:** 이미지/영상에서 visual token이 만들어지고 LLM 여러 층으로 들어가는 경로를 그린다.
- **구분할 점:** 영상 프레임을 입력한다는 것과 원본 영상의 모든 시각·음성 정보를 보존한다는 것은 다릅니다. 오디오 처리까지 목표라면 18번을 추가하세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [moovzi — Qwen3-VL 테크니컬 리포트](https://mvje.tistory.com/310) | DeepStack·MRoPE와 학습 전략을 정리. | 본문 |
| 한국어 · 논문 리뷰 | [Joanne Kim — Qwen3-VL Technical Report](https://joannekim0420.tistory.com/m/179) | 구조 변경과 영상 timestamp를 다른 설명으로 복습. | 본문 |
| 영어 · 공식 구조·사용 개요 | [QwenLM — Qwen3-VL](https://github.com/QwenLM/Qwen3-VL) | 공식 architecture 개요와 이미지·영상 입력 예시. | 본문 |
| 영어 · 구조 해설 | [SnackOnAI — Qwen3-VL And The Multimodal Tax](https://www.snackonai.com/p/qwen3-vl-and-the-multimodal-tax) | DeepStack, interleaved MRoPE, timestamp와 token geometry를 연결. | 본문 |


## 학습 기록

- [11주차 계획](../../../curriculum/week-11/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
