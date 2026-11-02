# 손계산에서 공식 구현 대조까지

[환경 준비](ENVIRONMENT.md) · [구현 기준](IMPLEMENTATION.md) · [15주 워크북](../curriculum/03-multimodal/week-15/WORKBOOK.md)

## 비교 순서

| 단계 | 무엇을 비교하나 | 통과 증거 |
|---|---|---|
| 1. 손계산 | 정해진 작은 입력의 한 연산 | 아래/주차 워크북 기대값 |
| 2. 성질 | causal·shift·reset·shape | 바꾸면 안 되는 출력이 유지됨 |
| 3. 독립 구현 | 느린 순차식과 내 모듈 | 출력·마지막 상태 오차 |
| 4. 공식 모듈 | 같은 입력·가중치의 HF 부분 연산 | parameter 대응표와 오차 |
| 5. 층·전체 | 동일 설정의 통합 결과 | 처음 달라지는 중간 출력 |
| 6. 생성 | full 재계산과 cached decode | 매 token logits·상태 일치 |

기본 학습 완주에 전체 HF 모델 대조를 선행 조건으로 두지 않습니다. 다만 목표 구조의 충실한 재현을 주장할 때에는 수행한 비교 범위와 못 한 부분을 명확히 적습니다.

## 같은 seed만으로는 같은 가중치가 아니다

모듈 생성 순서·fused projection·초기화 규칙이 다르면 동일 seed에서도 값이 달라집니다. 먼저 한쪽의 작은 가중치를 고정하고 다른 쪽에 명시적으로 대응시킵니다. 이름만 같다고 복사하지 않고 shape와 축을 봅니다.

| 예시 대응 | 확인할 점 |
|---|---|
| 내 query projection ↔ 공식 q_proj | gate까지 묶였는지 확인; QSA q_proj는 query와 output gate를 함께 만듦 |
| 내 q/k/v ↔ GDN in_proj_qkv | concat 순서와 각 구간 폭 |
| 내 expert gate/up ↔ gate_up_proj | expert 축과 gate/up 순서 |
| 내 norm weight ↔ RMSNorm weight | 1+weight 방식과 직접 weight 방식 구분 |
| 내 hash table ↔ ngram embedding | hash seed·head별 modulus·offset·EOS 처리 |
| 내 vision position ↔ pos_embed | 격자 순서·보간 규칙 |

상세 shape는 [공식 코드](CODE_MAP.md)에서 읽습니다. 예시 이름을 모든 모듈에 공통으로 적용하지 않습니다.

## 비교 조건과 오차

- 동일 config·입력·mask·position·dtype·장치·eval 모드, dropout=0에서 시작합니다.
- 작은 FP32 실험의 초기 기준은 atol=1e-5, rtol=1e-4입니다. 이는 모든 길이·backend에 대한 보장값이 아닙니다.
- 각 원소에 대해 abs(actual−reference) ≤ atol + rtol×abs(reference)를 확인합니다.
- max absolute error와 처음 다른 token/층/연산을 기록합니다. near-zero 원소에서 relative error만 보고 판단하지 않습니다.
- top-k 비교는 먼저 동점 없는 입력으로 합니다. 동점은 tie 정책을 별도 기록합니다.
- 기준을 넘으면 Q/K/V→mask→weights→output→residual 순서로 좁힙니다. tolerance를 먼저 늘리지 않습니다.

## 반드시 포함할 입력

| 대상 | 입력/조건 | 기대 결과 |
|---|---|---|
| causal | 미래 token만 변경 | 이전 logits 불변 |
| QSA 경계 | r=4, 길이1/3/4/5/7/8/9 | complete block과 tail 구분 |
| QSA 실제 선택 | r=4,K=4,길이11,block 점수[0.1,0.9] | 선택 {4,5,6,7,8,9,10}, 제외 {0,1,2,3} |
| GDN | 같은 입력을 전체/여러 chunk로 처리 | 출력과 최종 matrix state 일치 |
| PLE | [A,B,EOS,C,D] | C가 이전 문서의 A/B를 사용하지 않음 |
| cache | prefill 3개 후 1개씩 생성 | full 재계산과 매 step 일치 |
| vision | 단일/두 이미지·순서가 다른 frame | patch 순서와 샘플 경계 보존 |
| 저장 | 같은 모델을 저장/복원 | eval 출력 일치 |

QSA 선택 집합은 **선택 단계 검사**입니다. 별도로 pooling·RoPE·score·GQA 출력도 검사합니다. 모든 block이 예산에 들어가는 경우만 테스트하면 pruning 오류를 놓칩니다.

## 결과 기록

| 항목 | 기록 |
|---|---|
| 연산·기준 source revision | |
| config·가중치 대응·입력 | |
| 기대값의 근거 | |
| 실제값·최대 오차·tolerance | |
| 실패/통과/미실행 | |
| 다음에 좁혀 볼 단계 | |

검증용 코드는 사용자가 직접 작성합니다. 이 문서는 완성 테스트 구현을 대신 제공하지 않으며, 아직 실행하지 않은 항목을 통과로 기록하지 않습니다.
