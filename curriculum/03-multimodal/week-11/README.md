# 11주차 — Qwen식 이미지·영상 위치와 processor

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 10주차 산출물 · 목표: 텍스트·이미지·영상의 위치 ID와 placeholder 규칙을 추적한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E07 · Qwen3-VL Technical Report](https://arxiv.org/abs/2511.21631) · [PDF](https://arxiv.org/pdf/2511.21631) | Qwen 계열의 이미지·영상 위치 처리 |

**읽는 범위:** Model Architecture 중 interleaved MRoPE와 video timestamp/입력 구성. DeepStack은 비교만 하고 구현 요구에서 제외.

**코드 독해 순서:** [Qwen4ExpTextRotaryEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L76) → [Qwen4ExpModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2010)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. t/h/w 위치를 한 축의 토큰 순서와 혼동하지 않는다. 텍스트 구간과 시각 구간에서 위치 ID를 만드는 규칙이 다르다.

2. timestamp 텍스트, frame sampling, temporal patching은 서로 다른 단계다. 영상이 길어진다고 frame을 무조건 모두 넣지 않는다.

3. Qwen3-VL 논문은 계보를 이해하기 위한 자료다. 목표 config의 deepstack_visual_indexes는 빈 목록이므로 DeepStack을 자동 이식하지 않는다.

## 손으로 확인하기

grid_thw=[2,4,4], spatial merge=2이면 merger 뒤 시각 token은 2×4×4/4=8개다. 텍스트 token 5개와 합친 위치 표를 만든다.

## 구현 순서

1. 작은 vocab·직접 만든 patch/placeholder로 입력 계약부터 확인한다. 다음 단계에서 고정 processor 출력을 덤프하고 공식 ID와 vocab 크기를 별도로 맞춘다.
2. get_rope_index/compute_3d_position_ids의 분기별 표를 만든다.
3. 단일 이미지, 두 이미지, 4프레임 영상, text-only 네 fixture를 정의한다.

**남길 산출물:** 멀티모달 입력 contract + 4개 processor fixture.

워크북의 합성 입력 경로부터 진행합니다. 공식 processor·전체 모델 대조는 기본 경로와 나누어 기록합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- placeholder token 수와 merger 출력 수가 일치해야 한다.
- padding을 넣은 batch와 단건 실행의 유효 위치 ID가 일관되어야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 같은 8개 token이라도 서로 다른 t/h/w 격자를 동일한 이미지로 처리해도 되는가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 논문 리뷰** — [moovzi — Qwen3-VL 테크니컬 리포트](https://mvje.tistory.com/310): DeepStack·MRoPE와 학습 전략을 정리.
- **영어 · 공식 구조·사용 개요** — [QwenLM — Qwen3-VL](https://github.com/QwenLM/Qwen3-VL): 공식 architecture 개요와 이미지·영상 입력 예시.

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

[이전 주](../../03-multimodal/week-10/README.md) · [다음 주](../../03-multimodal/week-12/README.md)
