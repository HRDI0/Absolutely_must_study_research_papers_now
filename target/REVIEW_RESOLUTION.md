# 커리큘럼 보강 결과

[홈](../README.md) · [보강 전 검수](CURRICULUM_REVIEW.md) · [시작 전 진단](../curriculum/START.md)

보강일: 2026-11-02. 필수 논문 8편·3단원·16주를 유지하고, 사용자가 직접 작은 모델을 구현할 때 막히기 쉬운 연결 단계를 보강했습니다. 모델 코드를 대신 작성하거나 실행하지 않았습니다.

## 지적과 보강 문서

| 검수에서 부족했던 점 | 추가한 학습 경로 | 사용자가 남길 증거 |
|---|---|---|
| 1. 선수 지식 진단 없음 | [START](../curriculum/START.md): shape·미분·CE·저장/복원 진단과 기초 강의 | 계산 답, 작은 학습·복원 결과 |
| 2. Annotated에서 decoder LM으로 도약 | [2주 워크북](../curriculum/01-basics/week-02/WORKBOOK.md): 유지/제거/변경 표, label shift, 5단계 | 문장 하나의 학습·생성, 확장 시 loss 해석 |
| 3. GDN·GR·PLE 난이도 급상승 | [5주](../curriculum/02-backbone/week-05/WORKBOOK.md) → [6주](../curriculum/02-backbone/week-06/WORKBOOK.md) → [8주](../curriculum/02-backbone/week-08/WORKBOOK.md) | recurrence 수치, gate·conv 분리, hash/EOS 표 |
| 4. QSA 검사가 토큰을 실제 제외하지 않음 | [7주 워크북](../curriculum/02-backbone/week-07/WORKBOOK.md): r=4, K=4, 길이 11, 점수 반전 | 선택 위치 변경, 제외된 V에 대한 출력 불변 |
| 5. 공식 비교의 환경·가중치 매핑 누락 | [환경](ENVIRONMENT.md) → [대조 절차](PARITY.md) → [15주](../curriculum/03-multimodal/week-15/WORKBOOK.md) | 버전·동일 가중치·첫 불일치·오차 표 |
| 6. synthetic vocab과 공식 ID 충돌 | [11주 워크북](../curriculum/03-multimodal/week-11/WORKBOOK.md): 합성 입력과 공식 processor 경로 분리 | vocab·special ID·placeholder·grid 계약 |
| 7. 시각 학습과 indexer 학습이 한 과제에 섞임 | [14주 워크북](../curriculum/03-multimodal/week-14/WORKBOOK.md) + [indexer 학습](../curriculum/03-multimodal/week-14/INDEXER_TRAINING.md) | 텍스트→이미지→영상→indexer의 독립 결과 |
| 8. 개념 해설을 구현 안내로 표시 | [선택 해설](../resources/EXPLANATIONS.md)와 5주 자료 설명 수정 | 개념은 해설, 구현 기준은 워크북·공식 코드로 확인 |

모든 주차에 워크북을 추가했습니다. 위 표에 없는 1·3·4·9·10·12·13·16주도 입력 shape, 작은 수치 예제, 실패 원인, 진행 단계 또는 완료 수준을 제공합니다.

## 공부하면서 사용하는 방법

1. 처음에는 [진단](../curriculum/START.md)과 [기본 LM 설정](TINY_CONFIG.md)까지만 준비합니다.
2. 각 주차 README에서 원문 범위를 읽고 WORKBOOK의 가장 작은 예제를 손으로 계산합니다.
3. 직접 구현하다 막히면 입력·예상값·실제값·시도한 방법을 GPT나 검색에 제시합니다. 완성 코드보다 첫 불일치의 원인과 힌트를 요청합니다.
4. 주차 README에서만 완료 상태를 체크합니다. 실패한 실험도 기록하고 다음 구성요소를 붙이기 전에 원인을 좁힙니다.
5. [16주 완료 수준](../curriculum/03-multimodal/week-16/WORKBOOK.md)에 따라 기본 완주, 목표 구조 통합, 공식 구현 대조를 구분합니다.

## 보강으로 해결하지 않은 것

- 실제 학습자가 이 자료만으로 구현을 끝낼 수 있는지 실사용 검증은 아직 하지 않았습니다.
- tiny config 생성, 공식 reference 설치·실행, 모델 학습, CPU/GPU 자원 측정은 수행하지 않았습니다. 설정과 시간 배분은 시작용 설계안입니다.
- 교육용 수치 예제가 공식 랜덤 초기화 결과와 같다는 뜻은 아닙니다. 정규화·gate·hash를 고정한 단독 검사를 먼저 합니다.
- 원본 학습 데이터·규모·성능 재현은 이 과정의 완료 조건이 아닙니다.

## 문서 길이와 이동

[작성 규칙](../curriculum/WRITING.md)에 따라 활성 Markdown은 **120줄과 6,000자 이내**로 유지합니다. 길어지면 표·절을 끊지 않고 별도 문서로 나누며 진입점과 돌아가기 링크를 붙입니다. 개인 노트도 같은 규칙으로 이어 씁니다. 기존 보관본에는 소급 적용하지 않습니다.
