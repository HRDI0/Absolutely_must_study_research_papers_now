# 8주차 — N-gram embedding과 텍스트 본체 조립

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**Qwen3.8-Flash-Next 텍스트 본체 · 선수: 7주차 산출물 · 목표: lexical memory까지 포함한 3 GDN : 1 QSA의 작은 backbone을 완성한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E08 · On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) | 최종 목표의 전체 설계 |

**읽는 범위:** §2.3과 공식 NGramEmbedding/PLELayer/DecoderLayer/TextModel. hash, EOS 경계, 주입 위치는 코드 기준.

**코드 독해 순서:** [Qwen4ExpTextNGramEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1072) → [Qwen4ExpTextPLELayer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1177) → [Qwen4ExpTextDecoderLayer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1252) → [Qwen4ExpTextModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1371)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. n-gram embedding은 토큰 ID 이력으로 주소를 만드는 lookup이다. 신경망 activation을 저장하는 KV cache와 구분한다.

2. EOS 뒤에서 이전 문서의 n-gram이 섞이지 않도록 history 경계를 처리한다. decode에서는 최근 ID와 convolution 상태가 필요하다.

3. config의 ple_layer_ids=[2]는 공식 DecoderLayer가 layer_idx+1과 비교한다. 0-based index 2에 잘못 주입하지 않는다.

## 손으로 확인하기

[A,B,EOS,C,D]에서 C 위치의 bigram/trigram을 이전 문서 AB와 이어 만들면 왜 틀리는지 적는다. 8층을 G,G,G,Q,G,G,G,Q로 배치한다.

## 구현 순서

1. 워크북의 작은 수로 hash·EOS 경계를 계산한 뒤 공식 multiplier·prime·offset 규칙으로 확장한다. 교육용 hash와 공식 hash를 구분한다.
2. PLE의 lookup·gate·convolution·주입을 각각 검사한 뒤 stream collapse와 8층 조립으로 확장한다.
3. official tokenizer를 쓰는 실행과 작은 합성 vocab을 쓰는 실습을 구분해 config에 기록한다.

**남길 산출물:** 완성된 tiny text backbone + 구성요소별 parity 기록.

워크북의 단독 연산을 확인한 뒤 [목표형 축소 설정](../../../target/TINY_CONFIG.md)으로 조립합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 전체 입력과 한 토큰씩 입력의 n-gram ID/embedding이 같아야 한다.
- EOS 경계 전후와 길이 1 prefix에서도 이전 문서의 history가 새지 않아야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** n-gram table을 줄인 구현이 유지하는 성질과 재현하지 못하는 성질은 무엇인가?

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

[이전 주](../../02-backbone/week-07/README.md) · [다음 주](../../03-multimodal/week-09/README.md)
