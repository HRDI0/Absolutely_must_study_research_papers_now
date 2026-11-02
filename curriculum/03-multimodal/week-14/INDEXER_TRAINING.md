# 14주 보조 실습 — QSA indexer 학습

[14주 워크북](WORKBOOK.md) · [주차 완료 기록](README.md#checklist)

## 1. KL부터 손으로 확인

teacher P=[0.75,0.25], student Q=[0.5,0.5]이면 KL(P∥Q)=0.75 ln1.5+0.25 ln0.5≈**0.130812**입니다. P=Q이면 0입니다. 자연로그를 사용합니다.

log-probability와 probability를 구분합니다. [PyTorch KLDivLoss](https://docs.pytorch.org/docs/stable/generated/torch.nn.KLDivLoss.html)의 기본 input은 log probability입니다. 일반 mean과 batchmean의 분모도 다릅니다. 이 과제에서는 유효 query별 KL을 만든 뒤 유효 query 수로 평균한다는 계약을 명시합니다.

## 2. Token teacher를 block 분포로 바꾸기

단순 예제로 r=2, complete token teacher=[0.1,0.4,0.2,0.3]을 둡니다. 각 block에서 max를 취하면 [0.4,0.3], 다시 정규화하면 **[4/7,3/7]**입니다. indexer key의 평균 pooling과 teacher target의 max pooling을 혼동하지 않습니다.

현재 query가 볼 수 없는 block은 teacher/student에서 둘 다 제외합니다. complete block이 하나도 없는 query는 KL 평균의 유효 query 수에서도 제외합니다. tail token은 core context에 포함되지만 이 block-level KL target과 구분합니다.

## 3. Teacher를 확보하는 순서

1. 7주차 core를 dense causal 모드로 실행해 attention 분포를 관찰할 수 있게 합니다. 작은 text 실험이 먼저 동작해야 합니다.
2. teacher의 attention head 분포를 모아 token 분포를 만들고 block pooling/재정규화를 적용합니다.
3. 이 target을 고정하고 gradient가 teacher로 흐르지 않도록 합니다. 저장한 고정 teacher 분포로 시작해도 됩니다.
4. 우선 indexer만 학습합니다. backbone weight가 바뀌지 않는지, indexer loss/gradient가 바뀌는지 확인합니다.
5. warm-up 후 sparse core와 backbone을 함께 학습하는 단계는 별도 실험으로 둡니다. selected block에서 target을 다시 정규화하는 규칙까지 원문과 맞춥니다.

여기서 teacher를 별도 대규모 pretrained 모델로 마련할 필요는 없습니다. 랜덤 teacher는 연산 검증에는 쓸 수 있지만 학습 품질 향상의 근거로 삼지 않습니다.

## 4. 검사와 실패 원인

| 검사 | 기대 |
|---|---|
| P=Q | KL≈0 |
| 고정 P, 학습 가능한 student | 충분한 최적화 후 target과의 차이가 감소하는지 기록 |
| warm-up | backbone은 고정, indexer parameter만 변화 |
| 미래 token 변경 | 현재 query의 target/선택에 영향 없음 |
| complete block 없음 | NaN 없이 해당 query를 loss 집계에서 제외 |

CE와 KL을 합칠 때 각 항의 정규화, 가중치, 적용 query 수를 기록합니다. 처음에는 CE를 끄고 KL 단독으로 확인한 뒤 합칩니다. top-k index 자체를 미분한다고 설명하지 않습니다.

원문 근거는 [QSA training details §2.1.2](https://arxiv.org/html/2608.30320v1)입니다. 대규모 원문 step 수를 작은 실습에 그대로 적용하지 않습니다. 구현과 학습 결과는 사용자가 기록합니다.

[워크북으로 돌아가기](WORKBOOK.md)
