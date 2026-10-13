# 커리큘럼 운영 안내

[저장소 홈](../README.md) · [진행표](../PROGRESS.md) · [전체 참고자료](../resources/reading-list.md)

## 기간과 전제

기본 과정은 **12주, 주당 6~8시간**을 가정합니다. 선택 심화는 필요한 주차만 추가합니다. 시작 날짜는 정하지 않았으므로 본인의 일정에 맞춰 진행표에 적습니다.

선수 지식은 행렬곱·내적·softmax·gradient descent·기본 신경망 학습입니다. 부족하면 준비 기간 1~2주를 두고 1주차 attention 예제와 함께 보충합니다. Python/PyTorch는 선택 실습에 유용하지만 필수 과제는 손계산·그림·표로도 수행할 수 있습니다.

## 매주 반복할 흐름

| 세션 | 예상 시간 | 할 일 | 남길 기록 |
|---|---|---|---|
| A · 개념 잡기 | 1~1.5시간 | 한국어 해설 1개, 원문 초록·그림·기여 확인 | 문제와 아이디어 3문장 |
| B · 구조 읽기 | 2~2.5시간 | 지정된 Architecture/Method와 핵심 수식, 영어 해설 1개 | tensor shape와 수식 풀이 |
| C · 확인하기 | 2~2.5시간 | 주차별 작은 과제, 핵심 ablation 1개 확인 | 계산·그림·비교표 |
| D · 설명·회고 | 1~1.5시간 | 자료를 덮고 5분 설명, 빈 부분 기록 | NOTES, 주차 회고, PROGRESS |

한 주에 2편이면 첫 논문의 개념을 둘째 논문의 비교 기준으로 사용합니다. 특히 **7주차·9주차**는 구조/학습 목적 중심으로 범위를 제한합니다. 모든 실험까지 읽고 싶다면 각 주를 2주로 나눕니다.

## 주차별 계획

| 주차 | 목표 | 상세 계획 |
|---|---|---|
| 1 | Q/K/V와 causal mask, encoder–decoder의 경로를 설명한다. | [Transformer의 데이터 흐름](week-01/README.md) |
| 2 | RoPE·RMSNorm·SwiGLU·Pre-Norm이 들어가는 위치를 이해한다. | [현대 decoder 블록과 위치 정보](week-02/README.md) |
| 3 | KV 저장량과 attention 실행 시 중간 메모리를 구분한다. | [KV cache와 GPU 메모리](week-03/README.md) |
| 4 | Attention 캐시 압축과 FFN expert 선택의 역할을 구분한다. | [MLA와 sparse MoE](week-04/README.md) |
| 5 | Kernel feature map, 누적 상태 S와 정규화 상태 Z를 이해한다. | [Linear Attention과 recurrent state](week-05/README.md) |
| 6 | 누적·감쇠·delta update의 차이를 설명한다. | [Gated DeltaNet의 기억 갱신](week-06/README.md) |
| 7 | DSA의 선택 과정과 Qwen3.8-Next의 모듈 조합을 비교한다. | [Sparse와 hybrid 구조 비교](week-07/README.md) |
| 8 | Patch embedding과 Transformer encoder의 입력 shape를 이해한다. | [이미지를 토큰으로 바꾸기](week-08/README.md) |
| 9 | CLIP과 SigLIP 2의 학습 목표 및 시각 특징을 비교한다. | [시각 표현의 정렬과 학습](week-09/README.md) |
| 10 | Projector와 두 단계 학습의 동결 범위를 이해한다. | [Vision encoder와 LLM 연결](week-10/README.md) |
| 11 | Merger·DeepStack·MRoPE·timestamp가 해결하는 문제를 이해한다. | [이미지·영상 VLM의 전체 구조](week-11/README.md) |
| 12 | LLM 효율화와 VLM 연결 구조를 자기 언어로 종합한다. | [통합 복습과 최종 결과물](week-12/README.md) |
| 13 · 선택 | Learnable query와 cross-attention을 통한 시각 정보 요약을 이해한다. | [선택: Projector와 Q-Former](week-13/README.md) |
| 14 · 선택 | KDA의 gating과 MLA layer의 역할을 이해한다. | [선택: KDA와 hybrid attention](week-14/README.md) |
| 15 · 선택 | Thinker–Talker, ARIA, 시간 정렬의 역할을 이해한다. | [선택: 음성·영상과 스트리밍](week-15/README.md) |
| 16 · 선택 | Gemma 4 12B의 입력 투영과 통합 backbone을 이해한다. | [선택: Encoder-free 비교](week-16/README.md) |

## 학습 상태를 기록하는 방법

- 주차 계획의 체크박스는 해당 주에 할 작업입니다.
- 논문 NOTES는 본인이 작성한 이해·근거·미해결 질문을 모읍니다.
- PROGRESS는 전체 상태를 한눈에 확인하는 표입니다.
- 같은 개념의 설명이 충돌하면 원논문 절·식·그림을 함께 기록합니다.
- 완료 질문을 설명하지 못했다면 다음 주 첫 세션에서 해당 부분을 복습합니다.

## 기본 과정의 최종 목표

[최종 프로젝트](../capstone/README.md)에서 LLM 구조 두 가지와 VLM 구조 한 가지를 공통 기준으로 비교합니다. 성능을 직접 재현해야 수료하는 과정은 아닙니다. 모델의 구조, 비용, 근거와 한계를 분리해서 설명하는 것이 목표입니다.
