# 필수 논문 8편 — Qwen3.8-Flash-Next에 필요한 부분만

[홈](../README.md) · [16주 순서](../curriculum/README.md)

**정독 4편(E01·E02·E04·E08), 필요한 부분 발췌 4편(E03·E05·E06·E07)**입니다. 정독 역시 지정한 핵심 절을 뜻합니다. 모든 부록·실험표·관련 논문까지 따라 읽을 필요는 없습니다.

| ID | 논문·원문 | 필요한 이유 | 읽는 깊이 | 연결 주차 |
|---|---|---|---|---|
| E01 | [Attention Is All You Need](https://arxiv.org/abs/1706.03762) · [PDF](https://arxiv.org/pdf/1706.03762) | Q/K/V, causal attention, FFN의 공통 언어 | §3 중심 정독 | 1~2주 |
| E02 | [RoFormer: Enhanced Transformer with Rotary Position Embedding](https://arxiv.org/abs/2104.09864) · [PDF](https://arxiv.org/pdf/2104.09864) | 위치 정보와 Q/K 회전 | RoPE 유도·성질만 정독 | 3주 |
| E03 | [GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints](https://arxiv.org/abs/2305.13245) · [PDF](https://arxiv.org/pdf/2305.13245) | QSA core의 head 공유와 KV 구조 | 방법과 구조 그림만 발췌 | 3주 |
| E04 | [Gated Delta Networks: Improving Mamba2 with Delta Rule](https://arxiv.org/abs/2412.06464) · [PDF](https://arxiv.org/pdf/2412.06464) | 고정 크기 메모리와 delta 갱신 | recurrence·block 구조 정독 | 5주 |
| E05 | [An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://arxiv.org/abs/2010.11929) · [PDF](https://arxiv.org/pdf/2010.11929) | 이미지→patch→Transformer | §3 architecture 발췌 | 9주 |
| E06 | [Visual Instruction Tuning](https://arxiv.org/abs/2304.08485) · [PDF](https://arxiv.org/pdf/2304.08485) | 시각 feature를 LM과 연결하는 학습 원리 | 연결 구조·2단계 학습 발췌 | 10·14주 |
| E07 | [Qwen3-VL Technical Report](https://arxiv.org/abs/2511.21631) · [PDF](https://arxiv.org/pdf/2511.21631) | Qwen 계열의 이미지·영상 위치 처리 | interleaved MRoPE·timestamp 중심 발췌 | 11주 |
| E08 | [On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) | 최종 목표의 전체 설계 | §2 정독, §3·4 선택 독해 | 6~8·14·16주 |

E08은 마지막에 한 번만 읽는 논문이 아닙니다. 6~8주에 각 구성요소를 구현하며 읽고, 14주에 학습 설계, 16주에 전체 흐름을 재독합니다. E07은 영상·위치 처리의 배경이며 **Qwen3-VL의 모든 구조가 목표 모델에 사용된다는 뜻은 아닙니다.**

## 해설은 막힐 때 선택

한국어·영어 해설 20개는 [논문별 선택 해설](EXPLANATIONS.md)에 있습니다. 같은 내용을 모두 읽을 필요는 없습니다. 기초 구현이 낯설면 [시작 전 진단](../curriculum/START.md)의 공식 튜토리얼과 강의부터 필요한 부분만 봅니다.

## 논문 대신 반드시 볼 공개 자료

| 자료 | 언제 | 왜 필요한가 |
|---|---|---|
| [Harvard Annotated Transformer](https://nlp.seas.harvard.edu/annotated-transformer/) | 1~2주 | attention·head 분할·mask·학습·생성 코드를 연결 |
| [목표 checkpoint config](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/config.json) | 4주부터 | head·layer·expert·vision 설정의 근거 |
| [목표 Transformers 구현](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py) | 4주부터 | MoE·GR·PLE와 실제 이미지/영상 경로 확인 |
| [processor 설정](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/preprocessor_config.json) · [video 설정](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/video_preprocessor_config.json) · [processor 코드](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen3_vl/processing_qwen3_vl.py) | 11~12주 | patch 순서·placeholder·timestamp 입력 재현 |

별도 논문을 추가하지 않는다고 MoE·cache·SFT를 생략하는 것은 아닙니다. 각각 4주·13주·14주에서 목표 구현과 작은 예제로 배웁니다.

## 이번 필수 경로에서 뺀 것과 이유

| 이전 자료 | 이번에 빼는 이유 |
|---|---|
| DeepSeek-V2/V3/V3.2, MLA, DSA, DeepSeek-V4.1, GLM, Kimi | 다른 모델의 설계까지 따라가면 목표가 늘어남. QSA는 목표 논문에서 직접 학습 |
| PPO·DPO·GRPO·R1·DAPO | 정책 최적화 연구를 재현하는 과제가 아님. CE·SFT·QSA distillation만 구현 |
| FlashAttention·PagedAttention·서빙·양자화 원논문 | 고성능 kernel·서비스 운영은 구조 reference 구현의 선행 조건이 아님. 15주에서 측정과 차이를 설명 |
| CLIP·SigLIP2·BLIP-2·Flamingo 전체 독해 | 시각 사전학습/connector의 모든 계보를 공부하지 않음. ViT·LLaVA·Qwen 위치 처리에 집중 |
| Qwen Omni·Whisper·EnCodec·음성 통합 | 선택한 목표는 이 과정에서 이미지·영상→텍스트로 한정 |
| Mamba·DeltaNet·Switch·mHC 별도 논문 | 필요한 상태 갱신·routing·residual 개념을 선택 논문과 공식 코드에서 설명 |

중요하지 않은 연구라는 의미는 아닙니다. 이 목표의 완료 조건을 만족시키는 데 직접 필요한 독해만 남겼습니다. 제외한 자료는 과정 완료 뒤에도 자동으로 추가 필독이 되지는 않습니다.
