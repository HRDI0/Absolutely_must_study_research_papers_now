# 2주차 — Annotated Transformer에서 작은 언어모델로

[전체 과정](../../README.md) · [필수 논문 8편](../../../resources/README.md) · [구현 기준](../../../target/IMPLEMENTATION.md)

**기초 연산과 작은 언어모델 · 선수: 1주차 산출물 · 목표: 코드의 호출 순서를 추적하고 next-token 학습·생성의 기준 구현을 만든다.**

**학습 순서:** 이 페이지에서 읽을 범위 확인 → [단계별 실습 워크북](WORKBOOK.md) → [이 페이지의 완료 체크](#checklist).

## 이번 주 읽을 범위

| 논문 | 이번에 읽을 목적 |
|---|---|
| [E01 · Attention Is All You Need](https://arxiv.org/abs/1706.03762) · [PDF](https://arxiv.org/pdf/1706.03762) | Q/K/V, causal attention, FFN의 공통 언어 |

**읽는 범위:** Annotated Transformer의 attention, MultiHeadedAttention, PositionwiseFeedForward, SublayerConnection, Batch, SimpleLossCompute, greedy_decode 순서. 번역 데이터 다운로드와 분산 학습은 제외.

**코드 독해 순서:** [Harvard 원문](https://nlp.seas.harvard.edu/annotated-transformer/)의 attention → MultiHeadedAttention → PositionwiseFeedForward → SublayerConnection → Batch → SimpleLossCompute → greedy_decode. 워크북의 전환 표로 encoder·cross-attention·Generator를 정리합니다.

공식 코드는 고정 revision의 학습 참고 자료입니다. 이 저장소에 해당 모듈이 구현되어 있다는 뜻은 아닙니다. 읽으면서 사용할 입력 shape, 반환값, cache 변경을 적습니다.

## 먼저 이해할 내용

1. attention 함수는 연산, MultiHeadedAttention은 projection·head 분할·병합을 담당한다. forward의 입력과 반환 shape를 먼저 적는다.

2. SublayerConnection을 보고 residual과 normalization의 순서를 확인한다. 현대 목표 구현의 RMSNorm·SwiGLU를 원래 LayerNorm·ReLU와 혼동하지 않는다.

3. 입력 tokens[:-1]로 tokens[1:]를 예측한다. loss를 계산할 때 padding과 이후의 이미지 placeholder는 정답에서 제외해야 한다.

## 손으로 확인하기

시퀀스 [BOS, A, B, EOS]에 대해 입력·정답·loss mask를 각각 3칸으로 써 본다. 정답을 입력과 같은 위치로 두면 어떤 누수가 생기는지 설명한다.

## 구현 순서

1. Harvard 코드의 각 함수 옆에 한국어로 입력/출력/역할 한 줄을 적는다.
2. encoder와 cross-attention을 제거한 decoder-only 학습 루프와 greedy 생성 루프를 만든다.
3. 고정 문장 하나로 label shift·loss·생성을 확인한 뒤 32개 합성 문장으로 늘린다. 같은 prefix에 여러 정답이 있으면 loss=0을 요구하지 않는다.

**남길 산출물:** Annotated 코드 독해 노트 + 작은 LM의 loss 곡선.

[시작 전 진단](../../START.md) 후 [첫 dense LM 설정](../../../target/TINY_CONFIG.md)만 사용합니다. 먼저 CPU FP32·batch 1·짧은 입력에서 시작합니다. 환경 준비는 [환경 안내](../../../target/ENVIRONMENT.md)를 참고합니다.

## 정답을 확인하는 방법

- 학습 가능한 작은 배치의 loss가 초기값보다 내려가고 gradient가 유한해야 한다.
- 모델 저장·재로딩 전후 eval logits가 같아야 한다.
- 실패한 경우 입력·seed·dtype·shape·실제/기대값을 함께 저장합니다. 실행되지 않은 검증은 통과로 기록하지 않습니다.

**생각할 질문:** greedy_decode의 반복과 학습 시 병렬 forward는 어떻게 같은 조건부 확률을 계산하는가?

## 해설 자료 — 막힐 때 하나씩 선택

- **한국어 · 그림 해설** — [The Illustrated Transformer 번역](https://nlpinkorean.github.io/illustrated-transformer/): 입문 첫 자료. 전체 구조에서 Q/K/V와 여러 head까지 그림으로 설명.
- **영어 · 그림 해설** — [Jay Alammar — The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/): 한국어 번역의 원문. encoder와 decoder의 데이터 흐름을 설명.
- **영어 · 영상·시각화** — [3Blue1Brown — Attention in transformers](https://www.3blue1brown.com/lessons/attention/): Q/K/V와 attention을 시각적으로 설명. 논문 전체 리뷰보다 개념 입문에 적합.

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

[이전 주](../../01-basics/week-01/README.md) · [다음 주](../../01-basics/week-03/README.md)
