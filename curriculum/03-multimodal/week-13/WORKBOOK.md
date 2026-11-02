# 13주 실습 워크북 — Hybrid cache와 멀티모달 생성

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 상태별로 소유자를 정하기

[구현 기준의 cache 표](../../../target/IMPLEMENTATION.md)를 보고 상태를 분리합니다. 새 요청은 모든 관련 state와 position 이력을 초기화해야 합니다. 원래 입력 IDs도 PLE 때문에 보존합니다.

| 상태 | 실습에서 기록할 shape |
|---|---|
| GDN matrix | [B,Hv,dk,dv] |
| GDN conv | [B,Cconv,history] — source의 저장 길이 규칙 명시 |
| QSA K/V | [B,Hkv,T,d] |
| indexer key | token 이력을 저장하는 reference shape |
| PLE IDs | 최근 n−1개 |
| PLE conv | dilation에 필요한 과거 9칸(기본 예) |

“cache”라는 tensor 하나로 모두 표현하지 않습니다. 내부적으로 conv가 현재 token을 포함해 저장하는지 여부도 명시합니다.

## 2. 같은 입력을 여러 번 나누기

[A,B,C,D,E]를 전체, [A]/[B,C,D,E], [A,B,C]/[D,E], 한 token씩의 네 방법으로 처리합니다. 새 모델 가중치를 만들지 않고 같은 eval 모델을 사용합니다.

매 방식에서 각 token 출력과 마지막 state가 같아야 합니다. QSA r=4에서는 3+2 분할이 block 완성 경계를 지나므로 특히 중요합니다. PLE는 EOS를 포함한 [A,EOS,C,D]로도 반복합니다.

## 3. 생성 비교의 두 기준

- **기준 실행:** 매번 전체 prefix와 필요한 visual embedding을 사용해 마지막 logits를 재계산합니다.
- **cache 실행:** prefill 이후 새 token만 전달하고 상태를 갱신합니다.

처음에는 두 실행에 **동일한 정해진 새 token**을 공급해 logits를 비교합니다. argmax가 한번 갈라진 뒤 서로 다른 prefix를 비교하면 이후 오차 원인을 판단할 수 없습니다. logits가 맞은 뒤 greedy 생성 token도 비교합니다.

## 4. 이미지 prefill

vision encoder 호출 횟수를 기록합니다. cache 생성에서 매 token마다 이미지를 다시 인코딩하지 않도록 설계하되, 기준 실행과 같은 visual feature가 사용되는지 먼저 확인합니다.

이미지가 없는 새 요청을 바로 이어서 넣어 이전 요청의 feature·position delta가 남지 않았는지 봅니다. 배치 두 요청 중 하나가 EOS로 끝나는 경우도 분리해 기록합니다.

## 5. 메모리 예제와 실패 힌트

B=1,Hv=3,dk=dv=32,FP32인 GDN matrix는 한 층당 1×3×32×32×4=12,288 bytes입니다. T를 늘려도 이 matrix의 shape는 같습니다. 그러나 QSA KV와 indexer history까지 포함한 전체 메모리는 고정이라고 할 수 없습니다.

block 경계에서만 틀리면 indexer pooling/tail을, 시작 부분만 틀리면 conv padding을, EOS 뒤만 틀리면 PLE segment와 request reset을 봅니다. 배치2는 batch1을 통과한 다음 추가합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
