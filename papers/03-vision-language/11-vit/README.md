# 11. ViT

**An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale** (2020)

[원문·서지 정보](https://arxiv.org/abs/2010.11929) · [PDF](https://arxiv.org/pdf/2010.11929) · [논문 목록](../../README.md)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 이미지를 patch token으로 바꾸고 Transformer encoder에 입력하는 구조.
- **읽을 부분:** Patch embedding, position embedding, CLS token, encoder. 이미지·patch 크기에 따른 토큰 수 계산.
- **완료 기준:** 224×224 이미지를 16×16 patch로 나눴을 때 196개 patch가 되는 이유를 계산한다.
- **구분할 점:** 최초 공개 2020년, ICLR 2021. 논문 제목의 16×16이 모든 ViT·VLM에 고정된 patch 크기를 뜻하지 않습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [CKtrace — ViT 리뷰](https://cktrace.tistory.com/86) | Patch embedding과 encoder의 전체 흐름을 설명. | 본문 |
| 한국어 · YouTube | [텐초 — Vision Transformer 파헤치기](https://www.youtube.com/watch?v=QkwNdgXcfkg) | 한국어 논문 리뷰 영상. 검색 메타데이터로 확인. | 영상 메타데이터 |
| 영어 · YouTube | [Yannic Kilcher — An Image is Worth 16x16 Words](https://www.youtube.com/watch?v=TrdevFK_am4) | 영어 논문 설명 영상. 전체 재생과 자막은 미검증. | 영상 메타데이터 |
| 영어 · 구현·그림 해설 | [Vision Transformer walkthrough notebook](https://github.com/nerminnuraydogan/vision-transformer) | 그림과 단계별 PyTorch 구현으로 모델 구성요소를 확인. | 본문 |


## 학습 기록

- [8주차 계획](../../../curriculum/week-08/README.md)
- [내 학습 노트](NOTES.md)
- [자료 검증 기준과 전체 자료집](../../../resources/reading-list.md)

README는 참고자료 안내이며, 직접 공부한 내용은 NOTES에 작성합니다.
