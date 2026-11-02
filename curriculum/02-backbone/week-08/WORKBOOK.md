# 8주 실습 워크북 — N-gram embedding과 텍스트 본체 조립

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. Hash → lookup → PLE → 전체 모델로 분리

현재 과제는 n-gram 주소 계산을 먼저 끝내고 그다음 PLE의 key/value projection과 gate·conv를 붙이는 것입니다. 8층 조립은 마지막 단계입니다. [공식 코드 지도](../../../target/CODE_MAP.md)의 NGramEmbedding → PLELayer → DecoderLayer를 순서대로 읽습니다.

## 2. 작은 hash 연산 예제

교육용 값으로 A=1,B=2,EOS=0,C=3,D=4, multipliers=[3,5,7], modulus=17을 사용합니다. current부터 이전 token 순서로 곱하고 bitwise XOR한 뒤 modulo를 취합니다. **주소 연산 이해용 값이며 공식 seed에서 나온 multiplier가 아닙니다.**

| 위치 | 참조 이력 | 계산 | ID |
|---|---|---|---:|
| B의 bigram | [2,1] | (2×3 XOR 1×5) mod17 = (6 XOR5) mod17 | 3 |
| C의 trigram | [3,0,0] | (9 XOR0 XOR0) mod17 | 9 |
| D의 trigram | [4,3,0] | (12 XOR15 XOR0) mod17 | 3 |

C는 EOS 이전의 A/B를 사용하지 않습니다. hash collision은 가능한 설계 특성입니다. 서로 다른 n-gram의 ID가 같다는 이유만으로 오류로 판정하지 않습니다.

공식 대조에서는 이 단순 값을 사용하지 않고 source의 multiplier 생성, head별 prime modulus, offset, 정수 overflow 의미를 동일하게 맞춥니다. 먼저 생성된 작은 metadata를 출력해 비교한 뒤 lookup 결과를 봅니다.

## 3. PLE 연산 계약

lookup을 이어 붙인 embedding → shared value와 branch별 key projection → branch state와 key의 정규화 내적 → gate → branch별 value 주입 → 정규화된 값의 dilated conv를 더합니다.

gate score가 0이면 sigmoid=1/2입니다. value=[2,4], conv 기여를 0으로 고정한 단위 예제의 주입값은 branch마다 [1,2]입니다. 실제 gate에는 내적의 signed-square-root 변환이 있으므로 임의로 생략하지 않습니다.

conv kernel=4,dilation=3이면 과거 history 길이는 (4−1)×3=9입니다. 현재 t에서 보는 위치는 t−9,t−6,t−3,t입니다. GDN conv의 history와 같다고 가정하지 않습니다.

## 4. 단계별 조립

| 단계 | 확인 결과 |
|---|---|
| A. hash ID | 위 연산·EOS·길이1 입력 |
| B. lookup | 같은 ID가 같은 vector를 반환 |
| C. PLE만 | gate0/conv0 예제, branch shape |
| D. 한 층 주입 | ple_layer_ids=[2]는 1-based 2층 |
| E. backbone | G,G,G,Q,G,G,G,Q와 각 층 MoE·GR |
| F. 분할 입력 | 전체/한 token씩의 ID·embedding·state 일치 |

전체 logits 비교가 어려우면 처음 다른 중간 tensor까지 돌아갑니다. tokenizer는 합성 사전 경로를 유지합니다. 원본 tokenizer는 후속 입력 계약 비교 때 별도로 다룹니다.

## 5. 실패 힌트

decode만 어긋나면 최근 token ID와 dilated conv history를 각각 확인합니다. EOS 뒤만 틀리면 segment 시작을 확인합니다. embedding 폭이 틀리면 n-gram 차수×head 수와 concat 순서를 확인합니다. layer index를 0-based와 1-based로 섞지 않습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
