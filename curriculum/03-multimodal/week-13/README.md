# 13주차 — Hybrid cache와 멀티모달 생성

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 12주차 산출물 · 목표: 서로 다른 상태를 함께 관리하고 prefill 이후 한 토큰씩 생성한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

**새 필독 논문 없음.** 이전 주에 읽은 개념을 공식 코드에 연결합니다.

**읽는 범위:** 공식 모델의 Cache 호출·generation position 처리. 새로운 cache 논문을 추가하지 않는다.

**코드 독해 순서:** [Qwen4ExpTextGatedDeltaNet](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L456) → [Qwen4ExpTextQSAIndexer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L665) → [Qwen4ExpTextNGramEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1072) → [Qwen4ExpForConditionalGeneration](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2397)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. GDN matrix state, short-conv state, QSA K/V 및 indexer history, PLE history는 갱신 주기가 다르다.

2. 이미지 encoder는 보통 prefill에서 수행하고 이후 생성은 이미 들어간 시각 feature를 문맥으로 사용한다. 매 token마다 영상을 다시 인코딩하지 않는다.

3. cache reset과 batch 분리가 없으면 다른 사용자의 문맥이 섞일 수 있다. 정답 비교 전에 상태 소유권부터 정한다.

## 손으로 확인하기

8층에서 GDN6층·QSA2층이다. 각 상태의 shape, dtype, 길이 증가 여부를 표로 쓰고 총 byte를 합산한다.

## 구현 순서

1. cache 구조체/문서에 state 종류와 reset 규칙을 명시한다.
2. 멀티모달 prefill 후 cached decode와 매번 전체 재계산을 비교한다.
3. 길이가 다른 두 요청을 단건/배치로 실행하고 종료 뒤 새 요청을 넣는다.

**남길 산출물:** hybrid cache 명세 + text/image/video generation 비교.

워크북의 합성 입력 경로부터 진행합니다. 공식 processor·전체 모델 대조는 기본 경로와 나누어 기록합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 분할 위치를 여러 곳으로 옮겨도 마지막 logits가 일치해야 한다.
- 새 요청에서 이전 GDN/PLE/indexer state가 남지 않아야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** QSA 토큰 예산이 일정해도 전체 모델의 상태 메모리가 O(1)이 아닌 이유는?

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

[이전 주](../../03-multimodal/week-12/README.md) · [다음 주](../../03-multimodal/week-14/README.md)
