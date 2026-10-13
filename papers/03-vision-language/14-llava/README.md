# 14. LLaVA

**Visual Instruction Tuning** (2023)

[원문·서지 정보](https://arxiv.org/abs/2304.08485) · [PDF](https://arxiv.org/pdf/2304.08485) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Vision encoder의 특징을 projector로 LLM에 연결하고 visual instruction tuning을 수행.
- **읽을 부분:** Vision encoder→projector→LLM, feature alignment와 instruction tuning의 두 단계.
- **완료 기준:** 각 학습 단계에서 어떤 모듈이 동결되고 어떤 모듈이 업데이트되는지 표시한다.
- **구분할 점:** 원래 LLaVA의 선형 projector와 후속 LLaVA-1.5의 MLP projector를 구분하세요. 공식 저장소와 문서에는 여러 세대 내용이 함께 있을 수 있습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — Visual Instruction Tuning](https://kimjy99.github.io/논문리뷰/llava/) | 구조와 instruction 데이터 생성·학습 단계를 따라 읽기. | 본문 |
| 한국어 · YouTube | [LLaVA Visual Instruction Tuning 논문 리뷰](https://www.youtube.com/watch?v=jm6He0Bad_o) | 한국어 영상으로 구조와 학습 흐름을 복습. | 영상 메타데이터 |
| 영어 · 공식 프로젝트 해설 | [LLaVA project page](https://llava-vl.github.io/) | 논문·그림·데모·공식 자료의 시작점. | 본문 |
| 영어 · 논문 해설 | [Intuitive Papers — Visual Instruction Tuning](https://intuitivepapers.ai/visual-instruction-tuning/) | 원래 연결 구조와 후속 1.5 변경을 함께 비교. | 본문 |
| 영어 · 공식 라이브러리 문서 | [Hugging Face — LLaVA](https://huggingface.co/docs/transformers/main/model_doc/llava) | 입력·추론 예시와 구현 확인. LLaVA-1.5 등 후속 모델 설명 포함. | 본문 |


## 학습 기록

- [10주차 계획](../../../curriculum/week-10/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
