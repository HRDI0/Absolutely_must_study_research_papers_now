# 3주 실습 워크북 — RoPE·GQA·KV cache

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. RoPE를 두 수로 확인하기

2차원 회전 R(θ)=[cosθ,−sinθ; sinθ,cosθ]를 사용합니다. q=(1,0), k=(1,0), θq=0, θk=π/2이면 회전 후 내적은 0입니다. 두 각도에 π/3을 더해도 위치 차이가 같으므로 내적은 0입니다. q 위치와 k 위치의 각도가 같으면 내적은 1입니다.

이 예제는 단일 주파수입니다. 실제 주파수 배열로 확장할 때 각 차원 쌍의 θ를 따로 계산합니다. partial RoPE에서는 회전 대상 밖의 값이 그대로인지 검사합니다.

## 2. GQA head 대응

Q heads=4, KV heads=2인 작은 예제로 먼저 시작합니다. query0/1은 KV0, query2/3은 KV1을 사용합니다.

| 단계 | shape |
|---|---|
| Q | [B,4,T,d] |
| 저장하는 K/V | [B,2,T,d] |
| 계산을 위해 공유한 K/V | 논리적으로 [B,4,T,d] |
| 출력 | [B,4,T,d] → [B,T,4d] |

KV0만 바꾸면 해당 group의 결과만 달라져야 합니다. 실제 메모리 측정에서는 repeat해서 확장한 tensor와 cache에 저장한 tensor를 구분합니다.

## 3. Cache offset 표

| 호출 | 새 입력 길이 | 과거 길이 | 새 위치 ID | 호출 후 KV 길이 |
|---|---:|---:|---|---:|
| prefill | 3 | 0 | [0,1,2] | 3 |
| decode1 | 1 | 3 | [3] | 4 |
| decode2 | 1 | 4 | [4] | 5 |

1-token query와 4-token key의 mask는 정사각형 mask가 아닙니다. 현재 query가 key0~3을 모두 볼 수 있어야 합니다. 회전된 K를 cache에 저장했다면 재사용 시 다시 회전하지 않습니다. 저장 규칙을 문서에 고정합니다.

## 4. 통과 순서

A. 손계산 회전 → B. partial 영역 검사 → C. GQA 공유 검사 → D. 캐시 없이 생성 → E. 같은 가중치로 cache 생성.

전체 prefix 재계산의 마지막 logits와 매 decode logits를 비교합니다. 우선 padding 없는 batch1에서 통과시킨 뒤 두 요청·padding을 추가합니다.

**오류 힌트:** rotate pair 순서, sin 부호, K 재회전, position0 반복, causal mask offset을 순서대로 확인합니다.

## 5. 자료에서 찾을 위치

RoFormer의 회전 내적 유도, GQA의 head 공유 그림을 읽습니다. 실제 목표의 partial/MRoPE는 [코드 지도](../../../target/CODE_MAP.md)의 RotaryEmbedding에서 나중에 대조합니다. 3주차에서 이미지 위치까지 한꺼번에 구현하지 않습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
