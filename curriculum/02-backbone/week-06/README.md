# 6주차 — Qwen식 GDN과 Gated Residual

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**Qwen3.8-Flash-Next 텍스트 본체 · 선수: 5주차 산출물 · 목표: 목표 모델 변형을 반영하고 4개 residual stream의 읽기/쓰기 경로를 구현한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E08 · On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) | 최종 목표의 전체 설계 |

**읽는 범위:** §2.1.1의 GDN parameterization, §2.2의 Gated Residual. 이전 GDN과 달라진 gate·norm을 공식 코드와 비교.

**코드 독해 순서:** [Qwen4ExpTextGatedDeltaNet](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L456) → [Qwen4ExpTextRMSNormGated](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L171) → [Qwen4ExpTextGatedResidual](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L995) → [Qwen4ExpTextDecoderLayer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1252)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. GDN 논문을 구현했다고 목표 모델 GDN까지 같아지는 것은 아니다. local convolution, Q/K normalization, output gate와 RMSNorm 정의를 확인한다.

2. GR의 branch 차원은 attention head 차원과 다르다. residual stream을 읽어 sublayer 입력을 만들고 결과를 branch별로 다시 주입한다.

3. attention과 MoE 앞에 각각 GR 경로가 있다. 최종 출력에서 여러 stream을 어떻게 줄이는지도 별도로 추적한다.

## 손으로 확인하기

B=1,T=4,D=128,branch=4이면 residual은 [1,4,512] 또는 논리적으로 [1,4,4,128]이다. sublayer 입력은 [1,4,128]로 돌아와야 한다.

## 구현 순서

1. Qwen식 GDN의 연산 순서를 코드 심벌별로 대조한다.
2. GR read → GDN/MoE → write 경로를 1층씩 구현한다.
3. branch 수 1인 대조 실험은 별도 ablation으로 표시하고 목표의 4-branch 구성과 섞지 않는다.

**남길 산출물:** Qwen GDN·GR 모듈 + 원형 대비 변경표.

워크북의 단독 연산을 확인한 뒤 [목표형 축소 설정](../../../target/TINY_CONFIG.md)으로 조립합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 워크북의 고정 gate로 GR read/write를 먼저 계산한다. 공식 모듈 대조 시 동일한 가중치와 normalization 정의를 사용한다.
- 1토큰/여러 토큰에서 convolution state와 GDN state를 각각 이어서 검사한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 여러 residual branch를 만든 이유와 여러 attention head를 만든 이유는 같은가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 상세 구조 리뷰** — [dontech — Qwen3.8-Flash-Next 테크 리포트](https://donmain.dev/issue/qwen3-8-flash-next/): 네 가지 아키텍처와 설계 실험을 정리.
- **영어 · 공식 블로그** — [Alibaba Cloud — A New Architecture, Towards Ultimate Cost-Efficiency](https://www.alibabacloud.com/blog/qwen3-8-flash-next-a-new-architecture-towardsultimate-cost-efficiency_603501): 팀의 모델 발표와 구성요소 개요.

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

[이전 주](../../02-backbone/week-05/README.md) · [다음 주](../../02-backbone/week-07/README.md)
