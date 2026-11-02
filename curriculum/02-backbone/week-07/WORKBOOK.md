# 7주 실습 워크북 — QSA의 indexer와 sparse core attention

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. Indexer와 core를 분리하기

indexer는 block을 고르고, core는 선택한 token의 K/V로 attention 출력을 만듭니다. 첫 실험은 **선택 단계만** 검사합니다. Q/K projection 학습을 동시에 붙이지 않습니다.

## 2. 실제로 일부를 제외하는 고정 예제

r=4, K=4, 보이는 token ID가 0~10, 완성 block 점수=[0.1,0.9]입니다.

| 항목 | 결과 |
|---|---|
| complete block0 | {0,1,2,3} |
| complete block1 | {4,5,6,7} |
| tail | {8,9,10} |
| 고를 complete block 수 | K/r=1 |
| 최종 선택 집합 | {4,5,6,7,8,9,10} |
| 제외 집합 | {0,1,2,3} |

tail은 별도로 포함하므로 최종 선택 token 수는 K보다 클 수 있습니다. score를 [0.9,0.1]로 뒤집으면 선택 집합은 {0,1,2,3,8,9,10}이어야 합니다. index를 정렬할지 여부와 **집합으로 같은가**를 구분합니다.

## 3. 점수 계산도 별도로 검사

position/norm 처리까지 끝난 query heads를 q0=[1,0], q1=[0,1]로 둡니다. block keys가 k0=[2,−1], k1=[0.5,0.5]라면 sum_h ReLU(qh·kb)는 [2,1]입니다. 이 reference의 1/√d scaling까지 적용하면 d=2에서 [√2,1/√2]입니다. 이는 이미 전처리된 벡터의 점수 단위 예제입니다.

실제 흐름은 raw key → block 평균 → norm → block 시작 위치의 partial RoPE → 점수입니다. 평균 전에 각 token을 다른 각도로 회전한 결과와 동일하다고 가정하지 않습니다.

## 4. Causal 경계 검사

| 보이는 token 수 | 완성 block 수 | tail 수 |
|---:|---:|---:|
| 1 | 0 | 1 |
| 3 | 0 | 3 |
| 4 | 1 | 0 |
| 5 | 1 | 1 |
| 9 | 2 | 1 |

query i는 끝 위치가 i 이하인 완성 block만 고릅니다. 아직 보이지 않는 key를 평균에 포함한 뒤 최종 mask만 causal로 만들어도 누수는 사라지지 않습니다. padding 없는 순서로 먼저 검사하고 padding/packed 입력은 이후 추가합니다.

## 5. Core attention으로 연결

고정 선택 집합 → 동일 Q/K/V에 sparse mask → core output을 느린 masked attention과 비교합니다. Q=K=0이고 선택된 V들이 [2,4]라면 출력은 3입니다. 선택되지 않은 V를 아주 크게 바꾸어도 그 query의 출력은 그대로여야 합니다.

처음에는 같은 key/value를 공유하는 GQA grouping만 추가하고 gate는 고정한 뒤, 최종 output gate를 붙입니다. dense mask 구현은 정확성 검사이며 sparse kernel speedup을 보장하지 않습니다.

## 6. 통과 순서와 힌트

선택 집합 → 점수 → pooling/위치 → core → 전체 순서로 통과합니다. 동점은 피한 fixture로 시작합니다. 예산은 r의 배수로 두고 일부 block이 실제로 탈락하는 길이도 검사합니다.

**힌트:** 잘못된 token이 남으면 block→token index 확장부터, 미래가 새면 압축할 key의 가시성부터, 값만 틀리면 GQA group·scale·output gate부터 확인합니다. 원문 §2.1.2와 [코드 지도](../../../target/CODE_MAP.md)를 대조합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
