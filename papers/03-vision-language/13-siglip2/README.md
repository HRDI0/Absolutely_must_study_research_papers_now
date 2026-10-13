# 13. SigLIP 2

**SigLIP 2: Multilingual Vision-Language Encoders with Improved Semantic Understanding, Localization, and Dense Features** (2025)

[원문·서지 정보](https://arxiv.org/abs/2502.14786) · [PDF](https://arxiv.org/pdf/2502.14786) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 추가
- **한 줄 설명:** CLIP 이후의 시각 표현 학습과 지역 특징·다국어·유연한 입력 해상도를 이해.
- **읽을 부분:** Sigmoid objective, captioning/localization, self-distillation, masked prediction, NaFlex.
- **완료 기준:** 전역 이미지 의미와 patch 단위 특징이 VLM에서 각각 왜 필요한지 설명한다.
- **구분할 점:** Qwen3-VL은 SigLIP-2에서 초기화한 시각 인코더를 추가 학습합니다. SigLIP 2의 NaFlex 전처리와 Qwen3-VL의 입력 처리가 모두 동일하다고 일반화하면 안 됩니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 부분 논문 리뷰 | [킹남지 — SigLIP 2](https://kingnamji.tistory.com/76) | Teacher–student 시각 인코더 학습 중심. 논문 전체 해설은 아님. | 본문 |
| 한국어 · 입력·특징 해설 | [Coders Blog — SigLIP 2와 NaFlex](https://codersblog.github.io/posts/siglip2-naflex-dense-features/) | 종횡비·해상도·dense feature를 연결해 설명. | 본문 |
| 영어 · 개념·실습 해설 | [Hugging Face — SigLIP 2](https://huggingface.co/blog/siglip2) | 학습 목적함수와 모델 사용 예시를 함께 설명. | 본문 |
| 영어 · 공식 구현 안내 | [Google Research — SigLIP 2 README](https://github.com/google-research/big_vision/blob/main/big_vision/configs/proj/image_text/README_siglip2.md) | 모델 변형·체크포인트·구현 확인용. | 본문 |


## 학습 기록

- [9주차 계획](../../../curriculum/week-09/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
