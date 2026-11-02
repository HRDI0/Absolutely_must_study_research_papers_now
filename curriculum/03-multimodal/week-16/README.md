# 16주차 — 한 모델의 재현 범위 확정과 최종 설명

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 15주차 산출물 · 목표: 이미지·영상 입력에서 텍스트 생성까지 설명하고 재현 증거를 한 보고서에 정리한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E08 · On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) | 최종 목표의 전체 설계 |

**읽는 범위:** 이미 읽은 §2를 전체 데이터 흐름으로 재독하고 §4에서 성능 주장과 학습 실험의 차이를 확인. 새 논문 없음.

**코드 독해 순서:** [Qwen4ExpForConditionalGeneration](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2397)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. 최종 결과는 원래 대규모 모델의 성능 복제가 아니라 공개 연산 구조를 축소해 구현한 학습용 모델이다.

2. MTP·Muon·대규모 pretraining·fused kernel·host n-gram offload는 구현 범위를 명시해야 한다. 코드에서 지원하지 않는 항목을 완료로 세지 않는다.

3. 한 장의 구조도와 실패 사례로 이해를 검증한다. 새 모델 비교나 논문 추가로 프로젝트 종료 기준을 바꾸지 않는다.

## 손으로 확인하기

32×32 이미지 한 장의 patch→merge→placeholder→8층→logits 흐름에 실제 shape를 모두 적는다. 영상의 경우 무엇이 달라지는지 덧붙인다.

## 구현 순서

1. 워크북의 기본 완주·목표 구조 통합·공식 대조 수준 중 달성한 범위를 명시하고 각 구성요소의 구현 파일·결과·미완료를 연결한다.
2. text-only·single-image·multi-image·video 예시와 의도적으로 실패하는 입력을 정리한다.
3. 처음 보는 사람에게 20분 동안 구조를 설명하고 스스로 질문 5개에 답한다.

**남길 산출물:** 최종 구조도 + 재현 보고서 + 재실행 안내.

[부분 연산 대조 절차](../../../target/PARITY.md)에 따라 실제 수행한 범위를 기록합니다. 공식 전체 모델의 수치 일치는 심화 목표입니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 다른 실행 환경에서 작은 fixture와 저장된 모델을 다시 실행할 절차가 있어야 한다.
- 빠진 구성요소가 있는데 '완전 재현'으로 표시된 항목이 없어야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 기존 Transformer의 어느 부분이 각각 GDN/QSA/MoE/GR/PLE/vision 경로로 바뀌었는가?

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

[이전 주](../../03-multimodal/week-15/README.md) · [최종 구현 기준](../../../target/IMPLEMENTATION.md#done)
