# 2주 실습 워크북 — Annotated Transformer에서 작은 언어모델로

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 기본 LM 설정과 첫 데이터

[기본 LM 설정](../../../target/TINY_CONFIG.md)의 1층 dense 모델로 시작합니다. 아직 Qwen형 norm·MoE·GDN은 도입하지 않습니다. 입력 문자 사전은 BOS=0, EOS=1, PAD=2, A=3, B=4, 나머지 ID=5~15입니다.

| 원본 | 입력 IDs | target IDs |
|---|---|---|
| BOS A B EOS | [0,3,4] | [3,4,1] |
| BOS B A EOS | [0,4,3] | [4,3,1] |

시작은 **한 문장 한 배치의 과적합**입니다. 이후 문장을 늘립니다. 여러 문장에 같은 prefix와 서로 다른 정답이 있으면 모든 위치의 loss가 0으로 갈 수 없다는 점을 구분합니다.

모든 16개 logits가 0이면 각 token CE는 ln16≈2.772589입니다. 이미 shift한 target에 다시 shift를 적용하지 않습니다. 처음에는 직접 CE 경로를 쓰고, HF loss를 사용할 때에는 그 함수의 label 규칙을 별도로 확인합니다.

## 2. Harvard에서 무엇을 바꿀까

| 원래 역할 | 작은 decoder-only에서 할 일 | 확인 지점 |
|---|---|---|
| EncoderDecoder·encode | source encoder 경로 제거 | 입력 sequence 하나만 받음 |
| DecoderLayer의 src_attn | cross-attention 경로 제거 | memory/src_mask 인자 필요 없음 |
| self_attn | causal self-attention 유지 | 미래 token 변경 검사 |
| SublayerConnection | pre-norm 순서를 명시해 유지 | 설명의 post-norm과 혼합하지 않음 |
| FFN | dense FFN 유지 | 토큰별 동일 함수 |
| embedding/position | 문자 embedding+고정 위치 정보 | [B,T,D] |
| Generator | raw logits를 반환하도록 설계 | CE 직전 softmax하지 않음 |
| Batch·SimpleLossCompute | 입력/target 1칸 shift와 유효 token 평균 | PAD는 정답에서 제외 |
| greedy_decode | prefix 전체→마지막 logits→argmax 반복 | EOS 또는 최대 길이에 종료 |

Harvard의 Generator는 log-softmax, 자체 학습 예제는 별도 loss 구성을 사용합니다. 그대로 섞지 말고 작은 LM의 raw-logits→CE 계약을 먼저 고정합니다. [원문](https://nlp.seas.harvard.edu/annotated-transformer/)

## 3. 단계를 나누기

1. **forward:** 위 입력 하나에서 [1,3,16] logits와 유한 loss를 얻습니다.
2. **학습:** 고정 문장을 반복해 초기 loss보다 충분히 낮아지는지 기록합니다. gradient 초기화·backward·optimizer update를 분리해서 설명합니다.
3. **생성:** 학습된 문장의 BOS부터 greedy 생성해 EOS까지 도달하는지 봅니다. 처음에는 cache 없이 매번 prefix 전체를 재계산합니다.
4. **복원:** config/사전/가중치를 저장하고 다시 읽어 같은 eval logits를 확인합니다.
5. **확장:** 문장을 늘리고 train/validation을 나눕니다. 한 문장 암기는 기능 검사이고 일반화 평가는 별도입니다.

일반화 결과가 나쁘다고 basic forward까지 틀렸다고 결론내리지 않습니다. 구현 검사와 데이터 문제를 나눕니다.

## 4. 실패 원인 찾기

| 증상 | 먼저 볼 것 |
|---|---|
| loss가 이상하게 낮게 시작 | 정답 token을 같은 위치 입력으로 주지 않았는가 |
| backward 뒤 gradient가 없음 | detach/no_grad/학습 제외 여부 |
| 업데이트해도 가중치 불변 | optimizer가 실제 module parameter를 받았는가 |
| 학습 문장은 맞지만 생성 실패 | teacher forcing과 실제 생성의 입력 차이 |
| 복원 뒤 출력이 다름 | config·사전·eval/dropout·가중치 저장 |

## 5. 설명과 영상

[Karpathy 강좌 목차](https://karpathy.ai/zero-to-hero.html)의 makemore로 next-token 학습을 확인한 뒤 [Let's build GPT](https://www.youtube.com/watch?v=kCc8FmEb1nY)를 봅니다. 이미 가능한 부분은 건너뛰고 위 전환표에서 막힌 부분만 질문합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
