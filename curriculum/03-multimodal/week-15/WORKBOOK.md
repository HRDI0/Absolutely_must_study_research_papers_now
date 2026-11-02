# 15주 실습 워크북 — 공식 구현 대조와 시간·메모리 측정

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 대조 환경 먼저 고정

[환경 준비](../../../target/ENVIRONMENT.md) → [가중치 대응·오차 규칙](../../../target/PARITY.md)을 읽습니다. 비교할 두 모델은 같은 config·동일 입력·동일 가중치입니다. 학습된 모델과 새 랜덤 모델을 같은 seed라는 이유로 비교하지 않습니다.

## 2. 작은 가중치 대응 연습

bias 없는 Linear의 weight가 [[1,0],[0,1],[1,1]], input=[2,3]이면 output=[2,3,5]입니다. 한쪽이 [out,in], 다른 쪽이 [in,out]로 저장한다면 전치가 필요합니다.

이 하나가 맞은 뒤 q/k/v, gate, norm, expert처럼 구조가 다른 parameter에 대응표를 만듭니다. matched/unmatched 목록을 남기고 누락 parameter를 무시하지 않습니다.

## 3. 어디서부터 다른가

| 단계 | 비교 tensor |
|---|---|
| text | embedding→norm→projection |
| attention | Q/K/V→position→mask→weight→output gate |
| GDN | conv→q/k norm→gate→state→output |
| MoE/GR/PLE | 선택 expert·read/write·hash/lookup |
| vision | patch→position→block→merger |
| 통합 | 각 layer output→final norm→logits |
| decode | 한 step logits와 변경 state |

최종 logits부터 보고 모든 모듈을 동시에 수정하지 않습니다. first mismatch를 찾은 뒤 상위 단계는 잠시 고정합니다.

## 4. 오차 수치 읽기

reference=2,actual=2.00001이면 absolute error=0.00001입니다. atol=1e-5,rtol=1e-4에서 허용치는 0.00021이므로 이 원소는 통과합니다. reference≈0에서는 absolute error가 중요합니다.

FP32와 BF16, eager와 고속 backend는 별도 실험으로 기록합니다. 다른 config나 mask의 출력을 tolerance로 덮지 않습니다. 같은 token을 공급한 full/cached 결과가 맞은 뒤 생성 token도 비교합니다.

## 5. 시간·메모리 기록

길이32/64/128/256, 같은 batch/dtype에서 prefill과 token decode를 분리합니다. 장치, warm-up 횟수, 반복 수, 평균/분산, peak memory를 기록합니다. GPU라면 동기화 시점을 적고 측정합니다.

tensor 저장량은 numel×element_size로 계산하고 allocator peak와 구분합니다. Python loop GDN, dense mask QSA의 측정으로 논문의 fused kernel 가속을 재현했다고 쓰지 않습니다.

## 6. 완료와 미검증

실행 가능한 모듈 대조부터 결과를 남깁니다. reference backend가 지원되지 않거나 환경 준비가 안 된 항목은 미검증입니다. 다른 설치 환경에서 재실행하기 전에는 호환성을 확인했다고 쓰지 않습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
