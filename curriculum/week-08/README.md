# 8주차 · 이미지를 토큰으로 바꾸기

[전체 커리큘럼](../README.md) · [진행표](../../PROGRESS.md) · [이전 주](../week-07/README.md) · [다음 주](../week-09/README.md)

**구분:** 기본 과정 · **권장 시간:** 6~8시간

## 이번 주 목표

Patch embedding과 Transformer encoder의 입력 shape를 이해한다.

## 읽을 논문과 범위

- [ViT](../../papers/03-vision-language/11-vit/README.md) · [작성할 노트](../../papers/03-vision-language/11-vit/NOTES.md)
  - 집중 범위: Patch embedding, position embedding, CLS token, encoder. 이미지·patch 크기에 따른 토큰 수 계산.

## 진행 순서

- [ ] 세션 A: 각 논문 폴더에서 한국어 해설을 하나 고르고 초록·구조 그림을 본다.
- [ ] 세션 B: 위 지정 범위와 핵심 수식에 집중하고 영어 자료로 보충한다.
- [ ] 세션 C: 아래 과제를 수행하고 가정·계산·근거를 남긴다.
- [ ] 세션 D: 자료 없이 5분 설명하고 완료 기준을 점검한다.
- [ ] 학습 노트·아래 회고·진행표를 업데이트한다.

## 확인 과제

224×224 이미지, patch 16×16에서 196개 patch를 계산한다. CLS token 포함 여부와 channel/embedding 차원을 표시한다.

**제출할 결과물:** 이미지→patch→embedding→encoder 그림

논문별 NOTES에 작성하고, 여러 논문을 비교하는 표는 이 문서의 아래 학습 기록에 남깁니다. 코드 실습을 추가한다면 [실습 안내](../../exercises/README.md)를 따릅니다.

## 완료 기준

- [ ] Patch 크기와 해상도 변화에 따른 token 수를 계산한다.
- [ ] 중요한 주장에 원논문 절·식·그림 또는 직접 계산한 조건을 남겼다.
- [ ] 아직 이해하지 못한 부분을 질문으로 적었다.

## 학습 기록과 회고

- 시작일:
- 완료일:
- 실제 공부 시간:
- 결과물 링크:
- 이번 주 핵심 설명:
- 헷갈렸던 개념:
- 원문에서 확인한 근거:
- 다음에 보충할 질문:
