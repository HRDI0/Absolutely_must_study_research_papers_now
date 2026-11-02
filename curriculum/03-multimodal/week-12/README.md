# 12주차 — 목표 Vision encoder·merger·영상 경로 구현

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 11주차 산출물 · 목표: 연습용 LLaVA projector를 목표 구조의 실제 시각 경로로 교체한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

**새 필독 논문 없음.** 이전 주에 읽은 개념을 공식 코드에 연결합니다.

**읽는 범위:** 새 논문 없음. pinned config와 공식 VisionPatchEmbed/VisionModel/VisionPatchMerger/Model의 forward.

**코드 독해 순서:** [Qwen4ExpVisionPatchEmbed](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1759) → [Qwen4ExpVisionModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1923) → [Qwen4ExpVisionPatchMerger](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1779) → [Qwen4ExpModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2010)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. 목표 patch embedding은 temporal 축까지 포함하는 Conv3d다. 정지 이미지의 temporal 입력 구성은 processor가 담당한다.

2. 시각 position embedding의 보간, rotary position, packed sequence 경계를 각각 구현한다.

3. merger 후 feature를 placeholder 위치에 삽입하고 원래 input_ids는 PLE 계산에도 전달한다. embedding만 넘기고 토큰 이력을 잃지 않는다.

## 손으로 확인하기

4프레임 64×64, temporal patch2, patch16, merge2이면 patch token32개, merger token8개다. 이미지 정규화와 frame padding은 별도 fixture로 검증한다.

## 구현 순서

1. processor 출력과 동일한 patch 순서를 유지한 Conv3d/vision block/merger를 구현한다.
2. text-only·image·video forward를 하나의 interface로 조립한다.
3. pixel 입력과 이미 계산한 visual feature를 받는 경로의 중복 계산을 확인한다.

**남길 산출물:** tiny multimodal forward + vision/merger parity 표.

워크북의 합성 입력 경로부터 진행합니다. 공식 processor·전체 모델 대조는 기본 경로와 나누어 기록합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 같은 작은 가중치에서 official vision/merger output과 비교한다.
- 텍스트와 이미지가 섞인 전체 forward에서 placeholder 치환 전후 shape와 위치를 검사한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 단순히 ViT와 LLM을 연결한 것에서 Qwen의 시각 경로 재현으로 넘어온 증거는 무엇인가?

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

[이전 주](../../03-multimodal/week-11/README.md) · [다음 주](../../03-multimodal/week-13/README.md)
