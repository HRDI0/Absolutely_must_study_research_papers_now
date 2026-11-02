# 3주차 — RoPE·GQA·KV cache

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**기초 연산과 작은 언어모델 · 선수: 2주차 산출물 · 목표: 위치와 head 공유를 구현하고 full forward와 cached decode의 일치를 확인한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E02 · RoFormer: Enhanced Transformer with Rotary Position Embedding](https://arxiv.org/abs/2104.09864) · [PDF](https://arxiv.org/pdf/2104.09864) | 위치 정보와 Q/K 회전 |
| [E03 · GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints](https://arxiv.org/abs/2305.13245) · [PDF](https://arxiv.org/pdf/2305.13245) | QSA core의 head 공유와 KV 구조 |

**읽는 범위:** RoFormer의 RoPE 유도와 회전 성질, GQA의 방법/구조 그림. RoFormer 전체 실험과 GQA checkpoint uptraining 재현은 제외.

**코드 독해 순서:** [Qwen4ExpTextRotaryEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L76) → [Qwen4ExpTextAttention](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L811)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. RoPE는 Q와 K의 부분 벡터에 위치별 회전을 적용한다. 상대 위치 효과는 두 회전의 내적에서 생긴다.

2. GQA는 여러 query head가 같은 K/V head를 이용한다. query 수와 KV 수를 별도 설정으로 관리해야 한다.

3. KV cache는 이전 K/V의 재사용이다. cache가 있어도 새 query는 필요한 과거 key와 비교하며, full attention 층의 상태는 길이에 따라 늘어난다.

## 손으로 확인하기

B=1,L=2,Hkv=1,T=128,d=32,FP32에서 K/V만 저장하면 2×1×2×1×128×32×4=65,536 bytes다. Q head 4개를 각각 저장하는 MHA와 비교한다.

## 구현 순서

1. 2차원 회전으로 RoPE를 먼저 구현한 뒤 partial RoPE로 확장한다.
2. MHA/MQA/GQA를 동일한 함수의 head 설정으로 비교한다.
3. prefill과 1토큰 decode를 분리하고 position offset을 cache 길이와 연결한다.

**남길 산출물:** RoPE/GQA/KV 모듈 + cache 메모리 계산표.

워크북의 한 연산부터 확인한 뒤 기본 LM에 붙입니다. 8층 전체 설정은 아직 필요하지 않습니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 같은 위치 차이를 유지하며 Q/K 위치를 함께 이동했을 때 내적이 유지되는지 검사한다.
- dropout=0, 동일 가중치에서 전체 forward와 토큰별 decode logits를 비교한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** KV head 수를 줄이는 것과 QSA로 읽는 token 수를 줄이는 것은 어떻게 다른가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 논문 리뷰** — [JiYeop Kim — RoFormer](https://kimjy99.github.io/논문리뷰/roformer/): 수식과 위치 인코딩의 요구 조건을 따라 읽기.
- **한국어 · YouTube** — [딥러닝 논문읽기 모임 — RoFormer](https://www.youtube.com/watch?v=tRe2XHF6UbQ): 회전행렬을 이용하는 위치 임베딩 설명. 영상 전체 재생 검증은 하지 않음.
- **영어 · 단계별 유도** — [You could have designed state of the art positional encoding](https://huggingface.co/blog/designing-positional-encoding): 위치 인코딩의 요구 조건에서 시작해 RoPE까지 유도.
- **한국어 · 논문 리뷰** — [JiYeop Kim — GQA](https://kimjy99.github.io/논문리뷰/gqa/): head 공유와 체크포인트 변환을 논문 흐름대로 설명.
- **영어 · 수식·구현 해설** — [Pratham Patel — Grouped-Query Attention](https://prathamp.com/blog/attention-gqa/): 캐시 크기와 head 그룹 구성을 더 자세히 공부.

해설은 필수 추가 독서량이 아닙니다. 한국어 설명 하나로 이해되면 같은 내용의 영어 자료·영상을 모두 볼 필요는 없습니다. 블로그와 공식 코드가 다르면 목표 버전의 코드·config를 기준으로 차이를 기록합니다. 영상 전체 시청 여부와 재확인 범위는 [검증 기록](../../../target/VERIFICATION.md)을 참고합니다.

## 일주일 작업 배분

| 요일 | 시간 | 작업 | 남길 것 |
|---|---:|---|---|
| 월 | 4h | 위 독해 범위와 개념 설명 읽기 | 모르는 용어·수식 5개 이내 |
| 화 | 4h | 손계산과 shape 추적 | 계산표와 입력/출력 계약 |
| 수 | 4h | 구현 순서 1번 진행 | 가장 작은 동작 예제 |
| 목 | 4h | 구현 순서 2번 진행 | 중간 출력 비교 |
| 금 | 4h | 구현 순서 3번 진행 | 연결된 모듈과 실패 목록 |
| 토 | 8h | 손계산·경계값 대조, 가능한 범위의 reference 비교 | 재실행 가능한 검증 결과 |
| 일 | 8h | 질문에 답하기, 정리·미완료 보완 | 아래 체크와 학습 기록 |

주 36시간은 이전 학습량을 기준으로 한 배분 예시입니다. 16주는 마감이 아니라 순서입니다. 검증이 끝나지 않으면 다음 주로 넘기기보다 이 주를 연장합니다.

<a id="checklist"></a>
## 완료 체크 — 여기에서만 기록

- [ ] 읽을 범위와 필수 용어를 내 말로 설명했다.
- [ ] 손계산과 텐서 shape를 확인했다.
- [ ] 이번 주 구현 산출물을 만들었다.
- [ ] 명시한 정답 검사와 실패 사례를 기록했다.
- [ ] 생각할 질문에 답하고 목표 모델과의 차이를 적었다.

## 내 기록

기록이 길어지면 [문서 분리 규칙](../../WRITING.md)에 따라 같은 폴더의 `NOTES-01.md`부터 이어 씁니다. 체크 상태는 이 페이지에 유지합니다.

- 공부한 날짜 / 실제 시간:
- 구현 파일 또는 노트 경로:
- 검증 결과 / 실행 환경:
- 이해되지 않은 부분:
- 축소·생략한 사항:
- 다음에 다시 할 일:

[이전 주](../../01-basics/week-02/README.md) · [다음 주](../../01-basics/week-04/README.md)
