# 5주차 · Linear Attention과 recurrent state

[전체 커리큘럼](../README.md) · [진행표](../../PROGRESS.md) · [이전 주](../week-04/README.md) · [다음 주](../week-06/README.md)

**구분:** 기본 과정 · **권장 시간:** 6~8시간

## 이번 주 목표

Kernel feature map, 누적 상태 S와 정규화 상태 Z를 이해한다.

## 읽을 논문과 범위

- [Linear Attention](../../papers/02-efficient-llms/07-linear-attention/README.md) · [작성할 노트](../../papers/02-efficient-llms/07-linear-attention/NOTES.md)
  - 집중 범위: Linear Transformer 수식과 causal/recurrent 표현. 고정된 feature dimension에서 시퀀스 길이에 따른 비용을 봅니다.

## 진행 순서

- [ ] 세션 A: 각 논문 폴더에서 한국어 해설을 하나 고르고 초록·구조 그림을 본다.
- [ ] 세션 B: 위 지정 범위와 핵심 수식에 집중하고 영어 자료로 보충한다.
- [ ] 세션 C: 아래 과제를 수행하고 가정·계산·근거를 남긴다.
- [ ] 세션 D: 자료 없이 5분 설명하고 완료 기준을 점검한다.
- [ ] 학습 노트·아래 회고·진행표를 업데이트한다.

## 확인 과제

작은 양수 feature vector 3쌍으로 S와 Z를 차례로 갱신하고, 같은 kernel attention의 batch 계산 결과와 비교한다.

**제출할 결과물:** 상태 갱신 손계산 또는 작은 실습 노트

논문별 NOTES에 작성하고, 여러 논문을 비교하는 표는 이 문서의 아래 학습 기록에 남깁니다. 코드 실습을 추가한다면 [실습 안내](../../exercises/README.md)를 따릅니다.

## 완료 기준

- [ ] 고정된 state dimension에서 길이에 따른 비용과 표현상의 절충을 설명한다.
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
