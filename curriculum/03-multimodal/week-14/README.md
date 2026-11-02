# 14주차 — 작은 학습으로 모델이 정보를 사용하는지 검증

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 13주차 산출물 · 목표: 합성 이미지·영상 과제로 forward 연결을 넘어 학습 가능한 정보 경로를 확인한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E06 · Visual Instruction Tuning](https://arxiv.org/abs/2304.08485) · [PDF](https://arxiv.org/pdf/2304.08485) | 시각 feature를 LM과 연결하는 학습 원리 |
| [E08 · On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) | 최종 목표의 전체 설계 |

**읽는 범위:** LLaVA의 supervised alignment 개념, 목표 논문 QSA dense distillation→sparse training 및 §3 optimizer 구분. PPO/GRPO는 제외.

**코드 독해 순서:** [Qwen4ExpForConditionalGeneration](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2397)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. 잘 연결된 랜덤 모델은 곧바로 유능한 VLM이 되지 않는다. 구조 검증과 사전학습 성능 재현을 별도 결과로 기록한다.

2. 텍스트만으로 답을 맞힐 수 있는 데이터는 시각 경로를 검증하지 못한다. 같은 질문에 이미지에 따라 다른 답을 주고 image-shuffle 대조군을 둔다.

3. indexer에는 선택 index 자체를 미분하는 대신 teacher 분포를 이용한 별도 학습 경로가 필요하다. inference reference에 학습 loss가 없다고 생략하지 않는다.

## 손으로 확인하기

빨강/파랑 두 이미지에 같은 질문을 주고 정답을 서로 다르게 만든다. 학습/평가에 동일 이미지가 들어가지 않게 seed 목록을 나눈다.

## 구현 순서

1. 워크북 A(텍스트) → B(이미지) → C(영상) 순서로 독립 실험을 진행하고 각 train/validation seed를 분리한다.
2. CE·assistant label mask를 확인한 뒤 D인 [indexer 학습 워크시트](INDEXER_TRAINING.md)를 진행한다. teacher 생성과 backbone freeze를 먼저 확인한다.
3. AdamW로 기본 학습 가능성을 확인한다. Muon은 목표 recipe 비교 항목으로 읽고 별도 실험을 할 때만 추가한다.

**남길 산출물:** 소규모 학습 보고서 + leakage 점검 + 시각/영상 ablation.

워크북의 합성 입력 경로부터 진행합니다. 공식 processor·전체 모델 대조는 기본 경로와 나누어 기록합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 정상 이미지 조건이 image-shuffle 및 text-only 조건보다 나은지 수치로 비교한다.
- indexer warm-up에서 backbone freeze와 indexer gradient/loss 변화를 확인한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 훈련 샘플 암기, 구조의 올바른 구현, 일반화 성능을 각각 무엇으로 검증하는가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 상세 구조 리뷰** — [dontech — Qwen3.8-Flash-Next 테크 리포트](https://donmain.dev/issue/qwen3-8-flash-next/): 네 가지 아키텍처와 설계 실험을 정리.
- **영어 · 공식 블로그** — [Alibaba Cloud — A New Architecture, Towards Ultimate Cost-Efficiency](https://www.alibabacloud.com/blog/qwen3-8-flash-next-a-new-architecture-towardsultimate-cost-efficiency_603501): 팀의 모델 발표와 구성요소 개요.
- **한국어 · 논문 리뷰** — [JiYeop Kim — Visual Instruction Tuning](https://kimjy99.github.io/논문리뷰/llava/): 구조와 instruction 데이터 생성·학습 단계를 따라 읽기.
- **한국어 · YouTube** — [LLaVA Visual Instruction Tuning 논문 리뷰](https://www.youtube.com/watch?v=jm6He0Bad_o): 한국어 영상으로 구조와 학습 흐름을 복습.
- **영어 · 공식 프로젝트 해설** — [LLaVA project page](https://llava-vl.github.io/): 논문·그림·데모·공식 자료의 시작점.

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

[이전 주](../../03-multimodal/week-13/README.md) · [다음 주](../../03-multimodal/week-15/README.md)
