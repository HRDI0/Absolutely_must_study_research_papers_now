# 5주차 — Gated DeltaNet의 상태 갱신

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**Qwen3.8-Flash-Next 텍스트 본체 · 선수: 4주차 산출물 · 목표: 행렬 상태를 갱신하는 순차 기준 구현을 만들고 additive memory와 비교한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E04 · Gated Delta Networks: Improving Mamba2 with Delta Rule](https://arxiv.org/abs/2412.06464) · [PDF](https://arxiv.org/pdf/2412.06464) | 고정 크기 메모리와 delta 갱신 |

**읽는 범위:** gated delta rule, block diagram, recurrent/parallel 표현의 관계. 하드웨어 kernel과 모든 benchmark는 첫 독해에서 제외.

**코드 독해 순서:** [Qwen4ExpTextGatedDeltaNet](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L456)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. 상태는 key에서 value로 가는 작은 연상 메모리다. 망각 gate와 delta write gate는 서로 다른 일을 한다.

2. 현재 key로 상태가 이미 예측하는 value를 구한 뒤 정답 value와의 오차를 기록한다. 같은 key가 반복되어도 단순 outer product 누적과 다르게 동작한다.

3. 훈련을 병렬화한 수식과 토큰별 순차식은 같은 연산을 표현해야 한다. 처음에는 느린 순차 구현을 정답 기준으로 삼는다.

## 손으로 확인하기

스칼라 예제 S=2, α=0.5, β=0.25, k=1, v=3, q=2에서 감쇠 상태 1, 오차 2, 새 상태 1.5, 출력 3을 계산한다.

## 구현 순서

1. 단일 head 순차 갱신 → multi-head 갱신 순서로 구현한다.
2. α=1/β=0 등 극한값을 분석하되 실제 sigmoid 경로와 손계산용 고정 gate를 구분한다.
3. 길이를 늘려 recurrent state 저장량과 softmax KV 저장량을 비교한다.

**남길 산출물:** 순차 GDN reference + 상태 이동 그림.

워크북의 단독 연산을 확인한 뒤 [목표형 축소 설정](../../../target/TINY_CONFIG.md)으로 조립합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 스칼라 손계산과 출력/최종 상태가 일치해야 한다.
- 시퀀스를 두 조각으로 나눠 상태를 넘겨도 전체 순차 실행과 같아야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 고정 크기 상태가 긴 prefix의 모든 토큰을 정확히 복원한다는 뜻인가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어·영어 · 수식·논문 리뷰** — [Junwon Lee — Gated Delta Networks](https://romaanj.github.io/reviews/gated-deltanet/): 언어 전환을 제공하는 상세 리뷰. fast-weight와 상태 갱신 관점.
- **영어 · 개념 개요** — [Sebastian Raschka — Gated DeltaNet](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/): 핵심 아이디어를 설명하는 짧은 개요. 단계별 실습은 워크북과 공식 recurrence를 따른다.

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

[이전 주](../../01-basics/week-04/README.md) · [다음 주](../../02-backbone/week-06/README.md)
