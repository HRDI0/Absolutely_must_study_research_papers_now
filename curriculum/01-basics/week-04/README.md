# 4주차 — SwiGLU·MoE와 목표 모델 설정 읽기

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**기초 연산과 작은 언어모델 · 선수: 3주차 산출물 · 목표: dense FFN을 routed experts + shared expert로 바꾸고 8층 축소 설정을 고정한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

**새 필독 논문 없음.** 이전 주에 읽은 개념을 공식 코드에 연결합니다.

**읽는 범위:** 새 논문 없음. 목표 모델의 config와 router/expert/shared-expert 공식 코드를 읽는다. DeepSeek-V3 전체 독해로 확장하지 않는다.

**코드 독해 순서:** [Qwen4ExpTextMLP](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L896) → [Qwen4ExpTextTopKRouter](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L952) → [Qwen4ExpTextExperts](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L913) → [Qwen4ExpTextSparseMoeBlock](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L973)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. SwiGLU는 두 projection 중 하나를 SiLU로 변환한 뒤 원소별 곱을 만들고 출력 projection을 적용한다.

2. router는 토큰별 expert 확률과 top-k를 선택한다. 선택된 확률의 재정규화 여부가 최종 합산값을 바꾼다.

3. shared expert는 모든 토큰이 사용하지만 출력에는 별도 gate가 적용된다. 활성 파라미터가 작다고 전체 가중치 메모리가 사라지지는 않는다.

## 손으로 확인하기

router 확률 [0.5,0.3,0.15,0.05]에서 top-2 재정규화 결과 [0.625,0.375]를 계산한다. 두 expert 출력 [2,0],[0,4]의 가중합은 [1.25,1.5]다.

## 구현 순서

1. 먼저 Python 수준 expert 반복으로 정답 기준을 만든 후 grouped 실행을 별도 단계로 둔다.
2. shared expert gate를 포함하고 한 expert에만 몰리는 입력도 검사한다.
3. 축소 config의 head·expert·ngram·vision 차원 제약을 표로 고정한다.

**남길 산출물:** MoE 모듈 + 축소 설정 명세 + 활성/전체 파라미터 비교.

워크북의 한 연산부터 확인한 뒤 기본 LM에 붙입니다. 8층 전체 설정은 아직 필요하지 않습니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 수동 expert 가중합과 모듈 출력이 일치해야 한다.
- 선택되지 않은 expert의 기여가 없고 shared expert는 모든 유효 토큰에 적용되어야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** DeepSeek MoE의 routing 규칙을 이름이 비슷하다는 이유로 그대로 가져와도 되는가?

## 해설 자료 — 막힐 때 하나씩 선택

- [한국어 목표 모델 구조 해설](https://donmain.dev/issue/qwen3-8-flash-next/): 이번 주 구현할 부분만 찾습니다.
- [영어 공식 config](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/config.json)와 [구현](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py): shape와 실제 연산을 대조합니다.

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

[이전 주](../../01-basics/week-03/README.md) · [다음 주](../../02-backbone/week-05/README.md)
