# 9주차 — ViT로 이미지의 토큰화 이해

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**이미지·영상 연결과 통합 검증 · 선수: 8주차 산출물 · 목표: 이미지 패치가 시퀀스로 바뀌는 과정을 구현하고 위치를 보존한다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E05 · An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://arxiv.org/abs/2010.11929) · [PDF](https://arxiv.org/pdf/2010.11929) | 이미지→patch→Transformer |

**읽는 범위:** ViT §3의 patch embedding, position, Transformer encoder. 대규모 사전학습 재현과 classification benchmark는 제외.

**코드 독해 순서:** [Qwen4ExpVisionPatchEmbed](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1759) → [Qwen4ExpVisionBlock](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1892)

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. 이미지는 공간 격자를 갖는다. patch를 평탄화한 순서와 positional encoding의 순서가 일치해야 한다.

2. 시각 encoder의 self-attention은 이미지 내부를 양방향으로 볼 수 있다. 언어 decoder의 causal mask를 그대로 적용하지 않는다.

3. 원래 ViT의 CLS 분류 출력을 그대로 LLM에 하나만 넣는 것이 목표 연결 방식은 아니다. 목표에서는 공간 feature가 남아야 한다.

## 손으로 확인하기

32×32 RGB 이미지를 16×16 patch로 나누면 4개 patch이고 각 raw patch는 768개 수다. Dv=64 projection의 입출력 shape를 적는다.

## 구현 순서

1. 2D patchify의 행/열 순서를 색상 격자 이미지로 눈으로 검사한다.
2. 작은 ViT encoder를 구현하고 각 patch feature를 유지한다.
3. 다음 주 목표용 temporal patch=2와 원래 ViT의 차이를 미리 표시한다.

**남길 산출물:** patchify/ViT 모듈 + 이미지 좌표-토큰 대응표.

워크북의 합성 입력 경로부터 진행합니다. 공식 processor·전체 모델 대조는 기본 경로와 나누어 기록합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- patch를 역순으로 붙이거나 H/W를 바꿨을 때 fixture가 차이를 잡아야 한다.
- 다른 이미지의 patch가 packed batch attention으로 섞이지 않아야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** 이미지의 모든 patch에 같은 position ID를 주면 어떤 정보를 잃는가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 논문 리뷰** — [CKtrace — ViT 리뷰](https://cktrace.tistory.com/86): Patch embedding과 encoder의 전체 흐름을 설명.
- **한국어 · YouTube** — [텐초 — Vision Transformer 파헤치기](https://www.youtube.com/watch?v=QkwNdgXcfkg): 한국어 논문 리뷰 영상. 검색 메타데이터로 확인.
- **영어 · YouTube** — [Yannic Kilcher — An Image is Worth 16x16 Words](https://www.youtube.com/watch?v=TrdevFK_am4): 영어 논문 설명 영상. 전체 재생과 자막은 미검증.

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

[이전 주](../../02-backbone/week-08/README.md) · [다음 주](../../03-multimodal/week-10/README.md)
