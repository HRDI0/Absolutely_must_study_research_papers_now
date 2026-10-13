# 현대 LLM·VLM 구조 학습 로드맵

> **주 학습 15편 + 목적별 심화 4편** · 한국어·영어 해설 및 구현 자료 **79개 항목 / 78개 고유 URL**
>
> 검토 기준일: **2026-10-13 (한국 시간)** · 연도는 원칙적으로 arXiv 최초 공개 기준

모델의 이름을 외우는 대신 **어떤 모듈이 어떤 문제를 해결하고, 입력·상태·출력이 어떻게 흐르는지** 이해하기 위한 목록입니다. 최신 성능 순위표나 모든 모델 계열을 망라하는 목록은 아닙니다.

## 목차

- [1. 전체 학습표](#roadmap)
- [2. 목적별 읽기 경로](#paths)
- [3. 논문별 설명·해설 자료](#papers)
- [4. 목록 검증과 자주 생기는 오해](#verification)
- [5. 핵심 개념 비교표](#concepts)
- [6. 공부 체크리스트·노트 양식](#checklist)

### 자료를 고르는 방법

- **첫 독해:** 한국어 해설 1개 → 원논문 그림·Architecture/Method → 영어 해설 1개 순으로 읽습니다.
- 같은 개념을 서로 다르게 설명한 자료를 여러 개 넣었습니다. 전부 정독할 필요는 없습니다.
- **논문 리뷰·개념 해설·공식 구현·AI 요약·영상**을 구분했습니다. 공식 구현은 해설을 대체하는 자료보다 수식과 코드를 연결하는 자료입니다.
- **확인 표기:** `본문`은 웹 도구로 페이지 본문을 확인, `안내 페이지`는 영상 소개·목차를 확인, `영상 메타데이터`는 검색 제목·설명 중심 확인, `접근 제한`은 수집 도구로 전체 본문을 확인하지 못했다는 뜻입니다. 링크가 앞으로 계속 작동한다는 보장은 아닙니다.
- 영상 전체를 재생하거나 모든 구현을 실행한 것은 아닙니다. 해설의 모든 수식·수치를 검증한 것도 아니므로, **기술적 판단의 기준은 각 항목의 원논문**으로 둡니다.
- AI 요약은 명시적으로 표시했습니다. 독립적인 논문 리뷰를 대신하는 자료로 취급하지 않습니다.

<a id="roadmap"></a>
## 1. 전체 학습표

### 1단계 · Transformer에서 현대 LLM까지

**목표:** Attention, 위치 정보, decoder-only 블록, KV cache, GPU 메모리 최적화 구분.

| 순서 | 논문·상세 자료 | 연도 | 간략한 설명 |
|---|---|---|---|
| 1 | [Transformer](#paper-01) | 2017 | Q/K/V, Multi-Head Attention, FFN, residual, causal mask를 배우는 출발점. |
| 2 | [RoFormer / RoPE](#paper-02) | 2021 | Q·K의 회전으로 attention 내적에 상대 위치 차이를 반영하는 방법. |
| 3 | [LLaMA](#paper-03) **추가** | 2023 | 현대 LLM 블록의 Pre-Norm, RMSNorm, SwiGLU, RoPE 조합을 이해. |
| 4 | [GQA](#paper-04) | 2023 | 여러 Query head가 K/V head를 공유해 KV cache와 메모리 읽기 부담을 줄임. |
| 5 | [FlashAttention](#paper-05) | 2022 | Attention 수식의 의미를 유지하며 tiling과 online softmax로 GPU 메모리 이동을 줄임. |

### 2단계 · 현대 LLM의 효율적인 구조

**목표:** KV 표현 압축, sparse MoE, 선형 상태 갱신, sparse attention, hybrid 설계 구분.

| 순서 | 논문·상세 자료 | 연도 | 간략한 설명 |
|---|---|---|---|
| 6 | [DeepSeek-V3](#paper-06) | 2024 | MLA, sparse MoE, 부하 분산, Multi-Token Prediction을 결합한 LLM 구조. |
| 7 | [Linear Attention](#paper-07) **추가** | 2020 | Kernel feature map과 누적 상태로 선형 어텐션을 표현하는 기초. |
| 8 | [Gated DeltaNet](#paper-08) | 2024 | 기억을 감쇠하는 gate와 특정 연관 정보를 수정하는 delta rule을 결합. |
| 9 | [DeepSeek-V3.2 / DSA](#paper-09) | 2025 | Lightning Indexer와 token selection으로 중요한 과거 토큰에 sparse attention을 계산. |
| 10 | [Qwen3.8-Next](#paper-10) | 2026 | Gated DeltaNet·QSA·Gated Residual·n-gram embedding을 통합해 성능·효율·안정성을 비교. |

### 3단계 · 이미지·영상과 LLM의 결합

**목표:** Patch token → 시각 표현 학습 → Vision–Language 연결 → 현대 영상 VLM의 흐름 이해.

| 순서 | 논문·상세 자료 | 연도 | 간략한 설명 |
|---|---|---|---|
| 11 | [ViT](#paper-11) | 2020 | 이미지를 patch token으로 바꾸고 Transformer encoder에 입력하는 구조. |
| 12 | [CLIP](#paper-12) | 2021 | 이미지·텍스트 dual encoder를 대조학습하여 서로 대응하는 표현을 학습. |
| 13 | [SigLIP 2](#paper-13) **추가** | 2025 | CLIP 이후의 시각 표현 학습과 지역 특징·다국어·유연한 입력 해상도를 이해. |
| 14 | [LLaVA](#paper-14) | 2023 | Vision encoder의 특징을 projector로 LLM에 연결하고 visual instruction tuning을 수행. |
| 15 | [Qwen3-VL](#paper-15) | 2025 | SigLIP-2 기반 encoder·merger·DeepStack·Interleaved MRoPE·영상 timestamp를 통합. |

### 목적별 심화 · 필요한 경우만 추가

| 번호 | 논문·상세 자료 | 읽는 목적 | 읽는 시점 |
|---|---|---|---|
| 16 | [BLIP-2](#paper-16) **추가** | Projector와 Q-Former 비교 | LLaVA 이후 |
| 17 | [Kimi Linear](#paper-17) | Gated DeltaNet과 KDA·MLA 하이브리드 비교 | Gated DeltaNet 이후 |
| 18 | [Qwen3.5-Omni](#paper-18) | 음성·영상 이해와 스트리밍 음성 출력 | Qwen3-VL 이후 |
| 19 | [Gemma 4](#paper-19) | 12B의 encoder-free 입력과 encoder 기반 VLM 비교 | VLM 기본 경로 이후 |

### 기존 목록에서 변경한 이유

| 결정 | 논문 | 이유 |
|---|---|---|
| 주 학습에 추가 | LLaMA | RMSNorm·SwiGLU·Pre-Norm 등 현대 기본 블록을 묶어서 배우기 |
| 주 학습에 추가 | Transformers are RNNs | Gated DeltaNet에 앞서 선형 어텐션과 recurrent state를 이해하기 |
| 주 학습에 추가 | SigLIP 2 | CLIP과 Qwen3-VL 사이의 현대 시각 인코더 개념 보완 |
| 비교용 심화 추가 | BLIP-2 | 단순 projector 외의 Q-Former 연결 방식을 비교하기 |
| 주 학습에서 심화로 이동 | Kimi Linear | Gated DeltaNet·Qwen3.8-Next와 일부 개념이 겹쳐 첫 회독 부담 조정 |
| 주 학습에서 심화로 이동 | Qwen3.5-Omni | 음성 입력·생성까지 공부할 때 가치가 커짐 |
| 선택 유지 | Gemma 4 | 통합 입력 구조를 비교하는 심화 사례 |

기존 15편 중 완전히 버릴 논문은 없었습니다. 개념 중복과 목표 범위에 따라 우선순위를 조정했습니다. BERT·GPT-2·DeepSeek-R1은 이번 목록에 새로 넣지 않았습니다. 언어모델 역사나 추론 후학습을 확장할 때 별도 경로로 읽을 수 있습니다. Mamba/Mamba-2는 상태공간모델까지 깊게 공부할 때 보충하되, 이 경로에서는 7·8번을 연결 고리로 사용합니다.

<a id="paths"></a>
## 2. 목적별 읽기 경로

| 목표 | 추천 경로 |
|---|---|
| 전체를 체계적으로 학습 | **1→15** 순서, 이후 16→19 중 필요한 논문 선택 |
| 이미지·영상 VLM 구조를 먼저 이해 | **1→2→3→4→11→12→13→14→15**, 이후 LLM 효율화 5→10 복귀 |
| 긴 문맥·메모리 효율 | **1→2→3→4→5→6→7→8→9→10**, Kimi Linear 17 추가 |
| Vision–Language connector 비교 | **11→12→14→16→15→19**; LLM 기초를 이미 안다는 가정 |
| 음성·영상 통합 대화 시스템 | 주 학습 이후 **18을 필수로 승격**, 19와 입력 구조 비교 |

**선수 지식:** 행렬곱·내적·softmax·gradient descent·기본적인 신경망 학습. 수식이 어렵다면 먼저 tensor shape와 그림을 따라간 뒤 유도로 돌아옵니다.

<a id="papers"></a>
## 3. 논문별 설명·해설 자료

자료는 대체로 **한국어 입문/리뷰 → 영어 해설 → 공식 구현** 순으로 배치했습니다. 번호별 완료 기준은 원문을 보지 않고 설명할 수 있는지 확인하는 질문입니다.

---

<a id="paper-01"></a>
### 01. Transformer

**Attention Is All You Need** (2017)

[원문·서지 정보](https://arxiv.org/abs/1706.03762) · [PDF](https://arxiv.org/pdf/1706.03762) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Q/K/V, Multi-Head Attention, FFN, residual, causal mask를 배우는 출발점.
- **읽을 부분:** §3 Model Architecture. encoder–decoder, self-attention, cross-attention을 구분합니다.
- **완료 기준:** Q, K, V와 attention score의 텐서 크기를 적고 causal mask가 필요한 이유를 설명한다.
- **구분할 점:** 원논문은 encoder–decoder입니다. 현대 decoder-only LLM과의 차이는 3번 LLaMA에서 연결합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 그림 해설 | [The Illustrated Transformer 번역](https://nlpinkorean.github.io/illustrated-transformer/) | 입문 첫 자료. 전체 구조에서 Q/K/V와 여러 head까지 그림으로 설명. | 본문 |
| 한국어 · 학습 노트 | [The Illustrated Transformer 학습 정리](https://ch010104.tistory.com/m/306) | 같은 개념을 한국어로 다시 복습하는 용도. 위 번역과 내용이 겹침. | 본문 |
| 영어 · 그림 해설 | [Jay Alammar — The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) | 한국어 번역의 원문. encoder와 decoder의 데이터 흐름을 설명. | 본문 |
| 영어 · 영상·시각화 | [3Blue1Brown — Attention in transformers](https://www.3blue1brown.com/lessons/attention/) | Q/K/V와 attention을 시각적으로 설명. 논문 전체 리뷰보다 개념 입문에 적합. | 본문 |
| 영어 · 구현 해설 | [Harvard NLP — The Annotated Transformer](https://nlp.seas.harvard.edu/annotated-transformer/) | PyTorch 코드와 논문을 함께 읽는 자료. 수집 도구의 페이지 크기 제한으로 본문 전체 확인은 제한됨. | 접근 제한 |

---

<a id="paper-02"></a>
### 02. RoFormer / RoPE

**RoFormer: Enhanced Transformer with Rotary Position Embedding** (2021)

[원문·서지 정보](https://arxiv.org/abs/2104.09864) · [PDF](https://arxiv.org/pdf/2104.09864) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Q·K의 회전으로 attention 내적에 상대 위치 차이를 반영하는 방법.
- **읽을 부분:** RoPE의 회전행렬과 상대 위치 유도. 2차원 쌍을 회전시키는 예부터 시작합니다.
- **완료 기준:** 회전된 Q와 K의 내적에 두 위치의 차이가 나타나는 이유를 설명한다.
- **구분할 점:** RoPE를 사용한다고 훈련 길이 밖으로 자동으로 정확히 일반화하는 것은 아닙니다. 길이 확장은 별도 설계 문제입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — RoFormer](https://kimjy99.github.io/논문리뷰/roformer/) | 수식과 위치 인코딩의 요구 조건을 따라 읽기. | 본문 |
| 한국어 · YouTube | [딥러닝 논문읽기 모임 — RoFormer](https://www.youtube.com/watch?v=tRe2XHF6UbQ) | 회전행렬을 이용하는 위치 임베딩 설명. 영상 전체 재생 검증은 하지 않음. | 영상 메타데이터 |
| 한국어 · 수식 정리 | [DONGJU — RoPE 논문정리](https://dongju923.github.io/paper/RoPE/) | 회전행렬과 상대 위치 관계를 재확인하기 위한 자료. | 본문 |
| 영어 · 단계별 유도 | [You could have designed state of the art positional encoding](https://huggingface.co/blog/designing-positional-encoding) | 위치 인코딩의 요구 조건에서 시작해 RoPE까지 유도. | 본문 |
| 영어 · 직관 설명 | [RoPE Explained With Clock Hands](https://projectcozy.me/blog/rotary-embeddings-explained) | 시곗바늘 비유로 회전과 여러 주파수의 역할을 이해. | 본문 |

---

<a id="paper-03"></a>
### 03. LLaMA

**LLaMA: Open and Efficient Foundation Language Models** (2023)

[원문·서지 정보](https://arxiv.org/abs/2302.13971) · [PDF](https://arxiv.org/pdf/2302.13971) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 추가
- **한 줄 설명:** 현대 LLM 블록의 Pre-Norm, RMSNorm, SwiGLU, RoPE 조합을 이해.
- **읽을 부분:** §2.2 Architecture, 필요하면 §2.4 Efficient implementation. 데이터와 벤치마크는 첫 독해에서 훑습니다.
- **완료 기준:** 원래 Transformer 블록과 LLaMA 블록을 나란히 그려 차이를 표시한다.
- **구분할 점:** LLaMA 1의 논문입니다. Llama 2/3 해설에서 등장하는 GQA 등 후속 변경을 원논문의 기여와 혼동하지 마세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [Happy Jihye — LLaMA](https://happy-jihye.github.io/nlp/nlp-12/) | 아키텍처 변경과 효율적인 구현 항목을 빠르게 파악. | 본문 |
| 한국어 · 논문 리뷰 | [chips.ai — LLaMA](https://chips.it.kr/posts/LLaMA/) | 학습 접근과 아키텍처를 다른 설명으로 복습. | 본문 |
| 영어 · 코드 해설 | [The Annotated LLaMA](https://www.nishantbhansali.com/blog/the-annotated-llama) | LLaMA 계열 구성요소를 코드로 따라가기. 논문판과 구현판 차이도 확인. | 본문 |
| 영어 · 영상 안내 | [Yannic Kilcher — LLaMA Paper Explained](https://www.classcentral.com/course/youtube-llama-open-and-efficient-foundation-language-models-paper-explained-133644) | 영어 논문 설명 영상의 소개·목차 페이지. YouTube 직접 링크가 아닌 강의 안내. | 안내 페이지 |

---

<a id="paper-04"></a>
### 04. GQA

**GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints** (2023)

[원문·서지 정보](https://arxiv.org/abs/2305.13245) · [PDF](https://arxiv.org/pdf/2305.13245) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 여러 Query head가 K/V head를 공유해 KV cache와 메모리 읽기 부담을 줄임.
- **읽을 부분:** MHA→MQA/GQA 체크포인트 변환, head pooling, uptraining, 그룹 수에 따른 절충.
- **완료 기준:** Query head 32개, KV head 8개인 예에서 MHA 대비 KV cache 비율을 계산한다.
- **구분할 점:** 같은 layer 수·head dimension·dtype·문맥 길이를 가정하면 KV head 수 감소에 비례해 캐시가 줄어듭니다. 전체 모델 메모리가 같은 비율로 줄어드는 것은 아닙니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — GQA](https://kimjy99.github.io/논문리뷰/gqa/) | head 공유와 체크포인트 변환을 논문 흐름대로 설명. | 본문 |
| 한국어 · 개념 해설 | [IBM — 그룹화된 쿼리 어텐션](https://www.ibm.com/kr-ko/think/topics/grouped-query-attention) | MHA·MQA·GQA를 처음 비교할 때 읽기. | 본문 |
| 영어 · 개념 해설 | [Thinam Tamang — Grouped Query Attention](https://thinamxx.github.io/blog/posts/GQA/gqa.html) | 짧은 GQA 개념 설명과 구현 방향. | 본문 |
| 영어 · 수식·구현 해설 | [Pratham Patel — Grouped-Query Attention](https://prathamp.com/blog/attention-gqa/) | 캐시 크기와 head 그룹 구성을 더 자세히 공부. | 본문 |

---

<a id="paper-05"></a>
### 05. FlashAttention

**FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness** (2022)

[원문·서지 정보](https://arxiv.org/abs/2205.14135) · [PDF](https://arxiv.org/pdf/2205.14135) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Attention 수식의 의미를 유지하며 tiling과 online softmax로 GPU 메모리 이동을 줄임.
- **읽을 부분:** HBM/SRAM 계층, tiling, online softmax, backward 재계산. 첫 독해에서는 CUDA 세부 구현을 생략해도 됩니다.
- **완료 기준:** N×N attention 행렬을 HBM에 저장하지 않고 결과를 누적하는 방법을 설명한다.
- **구분할 점:** 기본 dense FlashAttention의 연산량은 여전히 시퀀스 길이에 대해 제곱입니다. exact는 수학적 알고리즘의 의미이며 부동소수점 비트 단위 동일성을 뜻하지 않습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 개념 해설 | [Zero Math AI — FlashAttention](https://zeromathai.com/flashattention/) | 메모리 병목과 online softmax의 직관을 먼저 익히기. | 본문 |
| 한국어 · 기술 노트 | [영삼넷 — 플래시 어텐션](https://www.youngsam.net/notes/flash-attention-io-aware-gpu-memory) | HBM/SRAM 이동과 중간 행렬 저장의 차이를 복습. | 본문 |
| 영어 · 저자 블로그 | [Tri Dao — Fast Transformer Training with Long Sequences](https://hazyresearch.stanford.edu/blog/2023-01-12-flashattention-long-sequences) | FlashAttention의 동기와 긴 시퀀스에서의 구현 개선. | 본문 |
| 영어 · 후속 연구 해설 | [Tri Dao / Together AI — FlashAttention-2](https://www.together.ai/blog/tri-dao-flash-attention) | 병렬화와 work partitioning으로 확장. 원 논문 이후의 심화 자료. | 본문 |

---

<a id="paper-06"></a>
### 06. DeepSeek-V3

**DeepSeek-V3 Technical Report** (2024)

[원문·서지 정보](https://arxiv.org/abs/2412.19437) · [PDF](https://arxiv.org/pdf/2412.19437) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** MLA, sparse MoE, 부하 분산, Multi-Token Prediction을 결합한 LLM 구조.
- **읽을 부분:** Architecture의 MLA, DeepSeekMoE, auxiliary-loss-free load balancing, MTP. FP8·DualPipe는 시스템 심화.
- **완료 기준:** MLA의 캐시 압축과 MoE의 선택적 연산이 각각 무엇을 줄이는지 구분한다.
- **구분할 점:** MLA와 DeepSeekMoE는 V2에서 검증된 구조입니다. V3가 모두 처음 발명했다고 읽지 마세요. MoE 활성 파라미터 수는 전체 가중치 저장량과 다릅니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 논문 리뷰 | [EmjayAhn — DeepSeek-V3 Technical Report](https://emjayahn.github.io/2025/03/24/DeepSeek-V3-Technical-Report-정리/) | MLA·MoE·MTP와 학습 시스템을 폭넓게 정리. | 본문 |
| 한국어 · 아키텍처 리뷰 | [아이펠 리서치 — DeepSeek-V3: Efficiency at Scale](https://choiwonjin.tistory.com/88?category=1369230) | 주요 아키텍처인 MLA·DeepSeekMoE·MTP 중심. | 본문 |
| 영어 · 구조 비교 해설 | [Sebastian Raschka — From DeepSeek V3 to V3.2](https://magazine.sebastianraschka.com/p/technical-deepseek) | MLA와 MoE를 이해하고 후속 버전까지 비교. | 본문 |
| 영어 · 공식 구조·구현 개요 | [DeepSeek-AI — DeepSeek-V3 README](https://github.com/deepseek-ai/DeepSeek-V3/blob/main/README.md) | 공식 architecture 설명과 실행 자료. 튜토리얼보다 사실 확인용. | 본문 |

---

<a id="paper-07"></a>
### 07. Linear Attention

**Transformers are RNNs: Fast Autoregressive Transformers with Linear Attention** (2020)

[원문·서지 정보](https://arxiv.org/abs/2006.16236) · [PDF](https://arxiv.org/pdf/2006.16236) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 추가
- **한 줄 설명:** Kernel feature map과 누적 상태로 선형 어텐션을 표현하는 기초.
- **읽을 부분:** Linear Transformer 수식과 causal/recurrent 표현. 고정된 feature dimension에서 시퀀스 길이에 따른 비용을 봅니다.
- **완료 기준:** 과거 토큰을 매번 읽는 방식과 상태 S·정규화 상태 Z를 갱신하는 방식을 비교한다.
- **구분할 점:** 일반 softmax attention을 아무 변화 없이 정확한 고정 크기 RNN으로 바꾸는 논문이라고 해석하면 안 됩니다. kernel feature map을 사용하는 attention을 다룹니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [용진테크 — Transformers are RNNs](https://jinyongyun.tistory.com/7) | 선형 어텐션 수식과 RNN 해석을 짧게 복습. | 본문 |
| 한국어 · AI 요약 | [Liner — Transformers are RNNs 퀵 리뷰](https://liner.com/ko/review/transformers-are-rnns-fast-autoregressive-transformers-with-linear-attention) | 연구 질문과 결과를 빠르게 훑는 보조 자료. 수식·수치는 원문과 대조. | 본문 |
| 영어 · 수학 해설 | [Data Processing Club — A Kernel Perspective](https://data-processing.club/transformers-are-rnns/) | kernel 관점에서 Transformer와 recurrent 표현의 관계를 설명. | 본문 |
| 영어 · 영상 안내 | [Yannic Kilcher — Transformers Are RNNs](https://www.classcentral.com/course/youtube-transformers-are-rnns-fast-autoregressive-transformers-with-linear-attention-paper-explained-126723) | kernel·선형화·RNN 연결을 다루는 영상 목차. 강의 안내 페이지. | 본문 |

---

<a id="paper-08"></a>
### 08. Gated DeltaNet

**Gated Delta Networks: Improving Mamba2 with Delta Rule** (2024)

[원문·서지 정보](https://arxiv.org/abs/2412.06464) · [PDF](https://arxiv.org/pdf/2412.06464) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 기억을 감쇠하는 gate와 특정 연관 정보를 수정하는 delta rule을 결합.
- **읽을 부분:** Gate와 delta rule의 역할, state update, chunkwise 병렬 학습. 7번을 먼저 읽습니다.
- **완료 기준:** 단순 누적 메모리, gated decay, delta update의 차이를 설명한다.
- **구분할 점:** 최초 공개는 2024년이며 ICLR 2025 논문입니다. 전체 하이브리드 모델에는 attention layer도 남을 수 있습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어·영어 · 수식·논문 리뷰 | [Junwon Lee — Gated Delta Networks](https://romaanj.github.io/reviews/gated-deltanet/) | 언어 전환을 제공하는 상세 리뷰. fast-weight와 상태 갱신 관점. | 본문 |
| 한국어 · 연결 개념 해설 | [Taekhyun Park — Online Learning](https://thrillcrazyer.github.io/blog/2026/online-learning/) | 온라인 최적화 관점에서 delta rule과 gated delta rule을 연결. 수학 심화. | 본문 |
| 영어 · 구현 안내 | [Sebastian Raschka — Gated DeltaNet](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/) | 작은 구현을 통해 상태 갱신을 따라가는 보조 자료. | 본문 |
| 영어 · 공식 구현 | [NVlabs — GatedDeltaNet](https://github.com/NVlabs/GatedDeltaNet) | 공식 README·학습 설정·코드와 논문 수식을 연결. | 본문 |

---

<a id="paper-09"></a>
### 09. DeepSeek-V3.2 / DSA

**DeepSeek-V3.2: Pushing the Frontier of Open Large Language Models** (2025)

[원문·서지 정보](https://arxiv.org/abs/2512.02556) · [PDF](https://arxiv.org/pdf/2512.02556) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Lightning Indexer와 token selection으로 중요한 과거 토큰에 sparse attention을 계산.
- **읽을 부분:** Architecture와 DSA. 구조 이해가 목표라면 후반 RL·agent 학습은 후순위.
- **완료 기준:** Indexer가 하는 일과 실제 attention이 하는 일을 분리해서 그린다.
- **구분할 점:** DSA가 KV cache를 없애는 것은 아닙니다. 선택 후 attention 비용뿐 아니라 indexer가 과거를 채점하는 비용도 고려해야 합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · AI 요약 | [Moonlight — DeepSeek-V3.2 리뷰](https://www.themoonlight.io/ko/review/deepseek-v32-pushing-the-frontier-of-open-large-language-models) | DSA·학습 단계를 한국어로 훑는 보조 요약. 원문 수식 대조 권장. | 본문 |
| 한국어 · AI 요약 | [Liner — DeepSeek-V3.2 퀵 리뷰](https://liner.com/ko/review/deepseekv32-pushing-frontier-open-large-language-models) | 연구 질문·방법·평가를 다른 요약으로 복습. | 본문 |
| 영어 · 상세 구조 해설 | [Sebastian Raschka — From DeepSeek V3 to V3.2](https://magazine.sebastianraschka.com/p/technical-deepseek) | MLA와 DSA의 변화, 구조와 RL 변화의 구분. | 본문 |
| 영어 · 구현 안내 | [DeepSeek Sparse Attention — From Scratch](https://sebastianraschka.com/llms-from-scratch/ch04/15_deepseek_sparse_attention/) | Lightning Indexer와 top-k 선택을 코드 관점으로 연결. | 본문 |

---

<a id="paper-10"></a>
### 10. Qwen3.8-Next

**On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability** (2026)

[원문·서지 정보](https://arxiv.org/abs/2608.30320) · [PDF](https://arxiv.org/pdf/2608.30320) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Gated DeltaNet·QSA·Gated Residual·n-gram embedding을 통합해 성능·효율·안정성을 비교.
- **읽을 부분:** 네 가지 구조 변화와 ablation. pretraining loss, downstream 성능, prefill/decode 비용을 구분.
- **완료 기준:** GDN·QSA·MoE·외부 embedding이 각각 담당하는 역할을 설명한다.
- **구분할 점:** 논문 제목은 Qwen3.8-Next Architecture, 설명 대상 모델은 Qwen3.8-Flash-Next입니다. host memory의 n-gram embedding은 검색 기반 RAG와 다릅니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 구조 리뷰 | [dontech — Qwen3.8-Flash-Next 테크 리포트](https://donmain.dev/issue/qwen3-8-flash-next/) | 네 가지 아키텍처와 설계 실험을 정리. | 본문 |
| 한국어 · 개요 해설 | [Let's be Developers — Qwen3.8-Flash-Next](https://changsroad.tistory.com/611) | 구조 변경을 짧게 재확인. 정식 논문보다 먼저 나온 모델 발표 해설. | 본문 |
| 영어 · 공식 블로그 | [Alibaba Cloud — A New Architecture, Towards Ultimate Cost-Efficiency](https://www.alibabacloud.com/blog/qwen3-8-flash-next-a-new-architecture-towardsultimate-cost-efficiency_603501) | 팀의 모델 발표와 구성요소 개요. | 본문 |
| 영어 · 구조 해설 | [Research Papers — GDN, QSA, Gated Residual, N-gram](https://researchpapers.dev/en/explained/qwen3-8-next-architecture/) | 전체 구조를 짧게 복습하는 설명. 사실 판단은 원논문 우선. | 본문 |

---

<a id="paper-11"></a>
### 11. ViT

**An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale** (2020)

[원문·서지 정보](https://arxiv.org/abs/2010.11929) · [PDF](https://arxiv.org/pdf/2010.11929) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 이미지를 patch token으로 바꾸고 Transformer encoder에 입력하는 구조.
- **읽을 부분:** Patch embedding, position embedding, CLS token, encoder. 이미지·patch 크기에 따른 토큰 수 계산.
- **완료 기준:** 224×224 이미지를 16×16 patch로 나눴을 때 196개 patch가 되는 이유를 계산한다.
- **구분할 점:** 최초 공개 2020년, ICLR 2021. 논문 제목의 16×16이 모든 ViT·VLM에 고정된 patch 크기를 뜻하지 않습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [CKtrace — ViT 리뷰](https://cktrace.tistory.com/86) | Patch embedding과 encoder의 전체 흐름을 설명. | 본문 |
| 한국어 · YouTube | [텐초 — Vision Transformer 파헤치기](https://www.youtube.com/watch?v=QkwNdgXcfkg) | 한국어 논문 리뷰 영상. 검색 메타데이터로 확인. | 영상 메타데이터 |
| 영어 · YouTube | [Yannic Kilcher — An Image is Worth 16x16 Words](https://www.youtube.com/watch?v=TrdevFK_am4) | 영어 논문 설명 영상. 전체 재생과 자막은 미검증. | 영상 메타데이터 |
| 영어 · 구현·그림 해설 | [Vision Transformer walkthrough notebook](https://github.com/nerminnuraydogan/vision-transformer) | 그림과 단계별 PyTorch 구현으로 모델 구성요소를 확인. | 본문 |

---

<a id="paper-12"></a>
### 12. CLIP

**Learning Transferable Visual Models From Natural Language Supervision** (2021)

[원문·서지 정보](https://arxiv.org/abs/2103.00020) · [PDF](https://arxiv.org/pdf/2103.00020) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** 이미지·텍스트 dual encoder를 대조학습하여 서로 대응하는 표현을 학습.
- **읽을 부분:** Image/text encoder, similarity matrix, contrastive loss, zero-shot classification.
- **완료 기준:** 학습의 정답 이미지–문장 쌍과 추론의 label prompt가 어떻게 다른지 설명한다.
- **구분할 점:** CLIP은 문장을 생성하는 대화 모델이 아닙니다. 이미지·텍스트 표현 정렬과 LLM에 visual token을 주입하는 작업은 구분해야 합니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [yuha933 — CLIP 논문 리뷰](https://yuha933.tistory.com/22) | 대조학습과 zero-shot transfer의 기본 흐름. | 본문 |
| 한국어 · AI 작성 해설 | [Akileo — 이미지와 텍스트를 잇는 CLIP의 발상](https://akileox.github.io/ai/learning-transferable-visual-models-from-natural-language-supervision/) | 직관적인 재설명. 페이지에 AI 분석·초안 작성이 명시된 보조 자료. | 본문 |
| 영어 · 공식 블로그 | [OpenAI — CLIP: Connecting text and images](https://openai.com/index/clip/) | 연구 동기·접근·한계를 함께 읽는 첫 영어 자료. | 본문 |
| 영어 · 영상 안내 | [Yannic Kilcher — OpenAI CLIP](https://www.classcentral.com/course/youtube-openai-clip-connectingtext-and-images-paper-explained-126691) | 대조 목적함수·encoder 선택·zero-shot을 다루는 영상 소개와 목차. | 안내 페이지 |

---

<a id="paper-13"></a>
### 13. SigLIP 2

**SigLIP 2: Multilingual Vision-Language Encoders with Improved Semantic Understanding, Localization, and Dense Features** (2025)

[원문·서지 정보](https://arxiv.org/abs/2502.14786) · [PDF](https://arxiv.org/pdf/2502.14786) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 추가
- **한 줄 설명:** CLIP 이후의 시각 표현 학습과 지역 특징·다국어·유연한 입력 해상도를 이해.
- **읽을 부분:** Sigmoid objective, captioning/localization, self-distillation, masked prediction, NaFlex.
- **완료 기준:** 전역 이미지 의미와 patch 단위 특징이 VLM에서 각각 왜 필요한지 설명한다.
- **구분할 점:** Qwen3-VL은 SigLIP-2에서 초기화한 시각 인코더를 추가 학습합니다. SigLIP 2의 NaFlex 전처리와 Qwen3-VL의 입력 처리가 모두 동일하다고 일반화하면 안 됩니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 부분 논문 리뷰 | [킹남지 — SigLIP 2](https://kingnamji.tistory.com/76) | Teacher–student 시각 인코더 학습 중심. 논문 전체 해설은 아님. | 본문 |
| 한국어 · 입력·특징 해설 | [Coders Blog — SigLIP 2와 NaFlex](https://codersblog.github.io/posts/siglip2-naflex-dense-features/) | 종횡비·해상도·dense feature를 연결해 설명. | 본문 |
| 영어 · 개념·실습 해설 | [Hugging Face — SigLIP 2](https://huggingface.co/blog/siglip2) | 학습 목적함수와 모델 사용 예시를 함께 설명. | 본문 |
| 영어 · 공식 구현 안내 | [Google Research — SigLIP 2 README](https://github.com/google-research/big_vision/blob/main/big_vision/configs/proj/image_text/README_siglip2.md) | 모델 변형·체크포인트·구현 확인용. | 본문 |

---

<a id="paper-14"></a>
### 14. LLaVA

**Visual Instruction Tuning** (2023)

[원문·서지 정보](https://arxiv.org/abs/2304.08485) · [PDF](https://arxiv.org/pdf/2304.08485) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** Vision encoder의 특징을 projector로 LLM에 연결하고 visual instruction tuning을 수행.
- **읽을 부분:** Vision encoder→projector→LLM, feature alignment와 instruction tuning의 두 단계.
- **완료 기준:** 각 학습 단계에서 어떤 모듈이 동결되고 어떤 모듈이 업데이트되는지 표시한다.
- **구분할 점:** 원래 LLaVA의 선형 projector와 후속 LLaVA-1.5의 MLP projector를 구분하세요. 공식 저장소와 문서에는 여러 세대 내용이 함께 있을 수 있습니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [JiYeop Kim — Visual Instruction Tuning](https://kimjy99.github.io/논문리뷰/llava/) | 구조와 instruction 데이터 생성·학습 단계를 따라 읽기. | 본문 |
| 한국어 · YouTube | [LLaVA Visual Instruction Tuning 논문 리뷰](https://www.youtube.com/watch?v=jm6He0Bad_o) | 한국어 영상으로 구조와 학습 흐름을 복습. | 영상 메타데이터 |
| 영어 · 공식 프로젝트 해설 | [LLaVA project page](https://llava-vl.github.io/) | 논문·그림·데모·공식 자료의 시작점. | 본문 |
| 영어 · 논문 해설 | [Intuitive Papers — Visual Instruction Tuning](https://intuitivepapers.ai/visual-instruction-tuning/) | 원래 연결 구조와 후속 1.5 변경을 함께 비교. | 본문 |
| 영어 · 공식 라이브러리 문서 | [Hugging Face — LLaVA](https://huggingface.co/docs/transformers/main/model_doc/llava) | 입력·추론 예시와 구현 확인. LLaVA-1.5 등 후속 모델 설명 포함. | 본문 |

---

<a id="paper-15"></a>
### 15. Qwen3-VL

**Qwen3-VL Technical Report** (2025)

[원문·서지 정보](https://arxiv.org/abs/2511.21631) · [PDF](https://arxiv.org/pdf/2511.21631) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 주 학습 · 유지
- **한 줄 설명:** SigLIP-2 기반 encoder·merger·DeepStack·Interleaved MRoPE·영상 timestamp를 통합.
- **읽을 부분:** §2 Model Architecture와 §3.1 Training Recipe. 영상은 frame sampling·timestamp·토큰 예산도 함께 봅니다.
- **완료 기준:** 이미지/영상에서 visual token이 만들어지고 LLM 여러 층으로 들어가는 경로를 그린다.
- **구분할 점:** 영상 프레임을 입력한다는 것과 원본 영상의 모든 시각·음성 정보를 보존한다는 것은 다릅니다. 오디오 처리까지 목표라면 18번을 추가하세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [moovzi — Qwen3-VL 테크니컬 리포트](https://mvje.tistory.com/310) | DeepStack·MRoPE와 학습 전략을 정리. | 본문 |
| 한국어 · 논문 리뷰 | [Joanne Kim — Qwen3-VL Technical Report](https://joannekim0420.tistory.com/m/179) | 구조 변경과 영상 timestamp를 다른 설명으로 복습. | 본문 |
| 영어 · 공식 구조·사용 개요 | [QwenLM — Qwen3-VL](https://github.com/QwenLM/Qwen3-VL) | 공식 architecture 개요와 이미지·영상 입력 예시. | 본문 |
| 영어 · 구조 해설 | [SnackOnAI — Qwen3-VL And The Multimodal Tax](https://www.snackonai.com/p/qwen3-vl-and-the-multimodal-tax) | DeepStack, interleaved MRoPE, timestamp와 token geometry를 연결. | 본문 |

---

<a id="paper-16"></a>
### 16. BLIP-2

**BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models** (2023)

[원문·서지 정보](https://arxiv.org/abs/2301.12597) · [PDF](https://arxiv.org/pdf/2301.12597) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 목적별 심화 · 심화 추가
- **한 줄 설명:** 동결된 vision encoder와 LLM 사이를 Q-Former로 연결하는 대안.
- **읽을 부분:** Q-Former의 learnable query, cross-attention, 두 단계 사전학습. LLaVA 다음 비교용.
- **완료 기준:** Projector와 Q-Former가 시각 정보를 LLM에 전달하는 방식의 차이를 설명한다.
- **구분할 점:** 모든 현대 VLM이 Q-Former를 쓰는 것은 아닙니다. Vision–Language 연결 구조의 설계 공간을 넓히기 위한 선택 자료입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 상세 논문 리뷰 | [Luna AI — BLIP-2](https://lunaleee.github.io/posts/blip-2/) | Q-Former와 representation/generative learning 단계를 설명. | 본문 |
| 한국어 · 구조 요약 | [BLIP2, Q-Former 요약 정리](https://grow-up-by-coding.tistory.com/entry/Q-Former-란-요약-정리) | Q-Former 구조를 짧게 복습하는 보조 자료. | 본문 |
| 영어 · 저자 참여 설명·실습 | [Hugging Face — Zero-shot image-to-text generation with BLIP-2](https://huggingface.co/blog/blip-2) | 구조 소개와 captioning·VQA 실습을 연결. | 본문 |
| 영어 · 공식 연구 블로그 | [Salesforce — BLIP-2](https://www.salesforce.com/blog/blip-2/) | 연구팀의 사전학습 전략과 모듈 연결 설명. | 본문 |

---

<a id="paper-17"></a>
### 17. Kimi Linear

**Kimi Linear: An Expressive, Efficient Attention Architecture** (2025)

[원문·서지 정보](https://arxiv.org/abs/2510.26692) · [PDF](https://arxiv.org/pdf/2510.26692) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 목적별 심화 · 필수→심화
- **한 줄 설명:** Gated DeltaNet을 발전시킨 KDA와 MLA를 결합한 하이브리드 비교 사례.
- **읽을 부분:** KDA의 channel-wise gating, KDA/MLA layer mix, state와 KV cache. 8번 이후 또는 10번과 비교.
- **완료 기준:** GDN의 gate와 KDA의 gate가 제어하는 단위를 비교한다.
- **구분할 점:** Linear라는 이름에도 전체 모델은 MLA layer를 포함한 하이브리드입니다. 전체 모델의 KV cache가 완전히 사라진다고 읽지 마세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [secrett2633 — Kimi Linear](https://blog.secrett2633.cloud/ai/review/2025-10-31-Kimi-Linear-An-Expressive-Efficient-Attention-Architecture) | KDA·하이브리드 구조·효율 결과의 개요. | 본문 |
| 한국어 · AI 요약 | [Moonlight — Kimi Linear](https://www.themoonlight.io/ko/review/kimi-linear-an-expressive-efficient-attention-architecture) | 설계 요소를 한국어로 다시 훑는 보조 자료. | 본문 |
| 영어 · 공식 구조·구현 개요 | [MoonshotAI — Kimi Linear](https://github.com/MoonshotAI/Kimi-Linear) | KDA와 MLA 비율, 모델 개요, 구현 자료 확인. | 본문 |
| 영어 · 비교 해설 | [Sebastian Raschka — Hybrid Attention](https://sebastianraschka.com/llm-architecture-gallery/hybrid-attention/) | Gated DeltaNet과 KDA를 하이브리드 패턴 안에서 비교. | 본문 |

---

<a id="paper-18"></a>
### 18. Qwen3.5-Omni

**Qwen3.5-Omni Technical Report** (2026)

[원문·서지 정보](https://arxiv.org/abs/2604.15804) · [PDF](https://arxiv.org/pdf/2604.15804) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 목적별 심화 · 필수→심화
- **한 줄 설명:** Thinker–Talker, Hybrid Attention MoE, ARIA로 시청각 이해와 스트리밍 음성 생성을 결합.
- **읽을 부분:** Thinker/Talker 역할, audio encoder, ARIA, streaming, audio-video 시간 정렬.
- **완료 기준:** 음성 이해와 음성 생성의 경로를 분리해 그리고 텍스트·음성 단위의 정렬 문제를 설명한다.
- **구분할 점:** 이미지·텍스트 VLM이 목표이면 선택, 음성·영상 대화 시스템이 목표이면 필수로 승격합니다. Qwen3-Omni와 Qwen3.5-Omni의 세부 구조를 혼동하지 마세요.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 논문 리뷰 | [dimensionstp — Qwen3.5-Omni Technical Report Review](https://dimensionstp.github.io/study-concept/qwen3_5_omni/) | Thinker–Talker, ARIA, 실시간 상호작용을 중심으로 읽기. | 본문 |
| 한국어 · AI 요약 | [Moonlight — Qwen3.5-Omni](https://www.themoonlight.io/ko/review/qwen35-omni-technical-report) | 한국어 보조 요약. 원논문의 모듈명·수치를 우선. | 본문 |
| 영어 · 논문 요약 | [Vlad Bogolin — Qwen3.5-Omni Technical Report](https://vladbogo.substack.com/p/qwen35-omni-technical-report) | 구조와 streaming alignment의 개요를 빠르게 읽기. | 본문 |
| 영어 · YouTube | [Qwen3.5-Omni: Advancing Native Omnimodal Intelligence](https://www.youtube.com/watch?v=USTNnYFokTk) | 영어 영상 보조 자료. 검색 제목·설명 확인에 한하며 전체 내용은 미검증. | 영상 메타데이터 |

---

<a id="paper-19"></a>
### 19. Gemma 4

**Gemma 4 Technical Report** (2026)

[원문·서지 정보](https://arxiv.org/abs/2607.02770) · [PDF](https://arxiv.org/pdf/2607.02770) · [전체 표로 돌아가기](#roadmap)

- **우선순위:** 목적별 심화 · 선택 유지
- **한 줄 설명:** Dense/MoE 계열과 특히 12B의 encoder-free 멀티모달 입력을 비교.
- **읽을 부분:** 12B 입력 경로와 다른 Gemma 4 변형의 차이. LLaVA·Qwen3-VL과 연결 방식 비교.
- **완료 기준:** 깊은 별도 encoder를 없앤 것과 patch/frame projection을 없앤 것이 왜 다른지 설명한다.
- **구분할 점:** Encoder-free 특징을 Gemma 4 전체에 일반화하지 마세요. 여기서 핵심 비교 대상은 12B입니다.

| 언어·형식 | 자료 | 읽는 목적 | 확인 |
|---|---|---|---|
| 한국어 · 구조 해설 | [Data Lab — 이미지·오디오를 바로 이해하는 Gemma 4 12B](https://datastudywithmomo.tistory.com/109) | Encoder-free의 의미와 입력 모듈을 한국어로 읽기. | 본문 |
| 한국어 · 구조·실행 분석 | [공밀레 — Gemma 4 12B 완전 분석](https://gong-mil-le.tistory.com/729) | 입력 구조와 로컬 실행 관점을 함께 설명. 하드웨어·성능 수치는 공식 자료와 별도 대조. | 본문 |
| 영어 · 공식 기술 해설 | [Google Developers — Gemma 4 12B Developer Guide](https://developers.googleblog.com/gemma-4-12b-the-developer-guide/) | Vision embedder와 audio wave projection 설명을 우선 읽기. | 본문 |
| 영어 · 공식 개요 | [Google — Introducing Gemma 4 12B](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemma-4-12b/) | 새 입력 구조의 동기와 전체 모델 개요. | 본문 |

---

<a id="verification"></a>
## 4. 목록 검증과 자주 생기는 오해

**원래 제시된 15편은 모두 실재하는 논문으로 확인했습니다.** 아래는 중요한 명칭·기여·범위의 보정입니다. 최초 공개일과 모델 출시일은 구분합니다.

| 항목 | 확인·보정 | 1차 근거 |
|---|---|---|
| Qwen3.8-Next | arXiv 최초 제출 **2026-08-31**. 논문 제목은 Qwen3.8-Next Architecture, 대상 모델은 Qwen3.8-Flash-Next | [원문](https://arxiv.org/abs/2608.30320) |
| Qwen3.5-Omni | arXiv 최초 제출 **2026-04-17**. Qwen3-Omni와 별개의 보고서 | [원문](https://arxiv.org/abs/2604.15804) |
| Gemma 4 | arXiv 최초 제출 **2026-07-02**. Encoder-free 설명은 특히 12B에 해당 | [원문](https://arxiv.org/abs/2607.02770) · [공식 12B 설명](https://developers.googleblog.com/gemma-4-12b-the-developer-guide/) |
| ViT | 최초 공개 2020년, ICLR 2021. 두 연도 표기가 모두 등장할 수 있음 | [원문](https://arxiv.org/abs/2010.11929) |
| Gated DeltaNet | 최초 공개 2024년, ICLR 2025 | [원문](https://arxiv.org/abs/2412.06464) · [공식 저장소](https://github.com/NVlabs/GatedDeltaNet) |
| DeepSeek-V3 | MLA·DeepSeekMoE는 V2에서 검증된 구조. V3의 load balancing·MTP 등과 기여를 구분 | [원문](https://arxiv.org/abs/2412.19437) |
| FlashAttention | 기본 dense attention의 제곱 연산량을 없애는 방법이 아님. 중간 행렬 저장·메모리 이동과 계산 순서를 개선 | [원문](https://arxiv.org/abs/2205.14135) |
| GQA·MLA·DSA | 각각 head 공유·잠재 표현 압축·attention 대상 선택. 서로 같은 기법이 아니며 일부는 결합 가능 | [GQA](https://arxiv.org/abs/2305.13245) · [V3](https://arxiv.org/abs/2412.19437) · [V3.2](https://arxiv.org/abs/2512.02556) |
| 선형 어텐션 | 고정된 상태 차원에서 시퀀스 길이에 따른 비용을 보는 것. 일반 softmax attention의 무손실 고정 상태 변환으로 일반화하지 않기 | [원문](https://arxiv.org/abs/2006.16236) |
| CLIP·LLaVA | CLIP은 표현 정렬을 학습하는 dual encoder. LLaVA는 시각 특징을 LLM에 연결해 생성형 instruction following을 수행 | [CLIP](https://arxiv.org/abs/2103.00020) · [LLaVA](https://arxiv.org/abs/2304.08485) |
| Qwen3-VL·SigLIP 2 | Qwen3-VL의 시각 인코더는 공식 SigLIP-2 체크포인트에서 초기화하고 동적 해상도로 추가 학습 | [Qwen3-VL §2](https://arxiv.org/html/2511.21631v1#S2) |
| Kimi Linear | KDA만 있는 모델이 아니라 MLA도 포함한 하이브리드. 전체 KV cache가 0이 되는 구조가 아님 | [원문](https://arxiv.org/abs/2510.26692) |

### 해설 자료를 읽을 때 확인할 것

- 벤치마크 개선은 특정 모델·데이터·하드웨어·문맥 길이에서의 결과입니다. “항상 더 빠르다/좋다”로 일반화하지 않습니다.
- Prefill의 비용과 decode의 비용을 구분합니다. KV cache 저장량, HBM 읽기량, FLOPs, 실제 지연 시간은 서로 다른 지표입니다.
- MoE는 활성 연산량을 줄여도 전체 가중치 저장·expert 통신 비용이 남습니다.
- 논문 이후 업데이트된 README에는 후속 모델 내용이 섞일 수 있습니다. 특히 LLaVA 1.0/1.5, LLaMA 1/Llama 3를 구분합니다.
- “공식”은 발표 주체를 나타냅니다. 발표된 성능 수치가 독립적으로 재현됐다는 뜻은 아닙니다.

<a id="concepts"></a>
## 5. 핵심 개념 비교표

| 개념 | 무엇을 바꾸는가 | 공부할 논문 |
|---|---|---|
| RoPE | Attention의 Q/K에 위치 정보를 반영하는 방식 | 2 |
| GQA | 여러 Query head가 공유하는 K/V head 수 | 4 |
| FlashAttention | 같은 attention을 GPU에서 실행하는 계산·메모리 접근 방식 | 5 |
| MLA | 캐시에 저장하는 K/V 관련 표현의 차원·구조 | 6 |
| MoE | 토큰별로 실행하는 FFN expert의 선택 | 6, 10 |
| Linear Attention | 과거 정보를 kernel 기반 누적 상태로 표현하는 방식 | 7 |
| Gated DeltaNet | 누적 상태의 감쇠와 특정 정보 갱신 방식 | 8 |
| DSA / QSA | Attention을 계산할 과거 토큰·블록의 선택 | 9, 10 |
| ViT | 이미지에서 patch token과 시각 특징을 만드는 방식 | 11 |
| CLIP / SigLIP 2 | 이미지·텍스트 및 시각 표현을 학습하는 목표 | 12, 13 |
| Projector / Q-Former | 시각 특징을 LLM에 전달하는 연결 모듈 | 14, 16 |
| DeepStack | 여러 깊이의 시각 특징을 LLM 내부에 전달하는 방식 | 15 |
| Thinker–Talker | 이해·추론과 스트리밍 음성 생성을 나누는 구성 | 18 |
| Encoder-free 입력 | 별도 깊은 시각·음성 인코더 대신 입력 투영으로 통합 backbone에 연결 | 19의 12B |

위 표는 학습용 분류입니다. 실제 모델은 여러 기법을 함께 사용하며, 각 행의 세부 근거는 해당 번호의 원문에 있습니다.

<a id="checklist"></a>
## 6. 공부 체크리스트·노트 양식

### 진행 현황

| 번호 | 논문 | 해설 읽기 | 원문 구조·수식 | 설명/그림 완성 |
|---|---|---|---|---|
| 1 | [Transformer](#paper-01) | ☐ | ☐ | ☐ |
| 2 | [RoFormer / RoPE](#paper-02) | ☐ | ☐ | ☐ |
| 3 | [LLaMA](#paper-03) | ☐ | ☐ | ☐ |
| 4 | [GQA](#paper-04) | ☐ | ☐ | ☐ |
| 5 | [FlashAttention](#paper-05) | ☐ | ☐ | ☐ |
| 6 | [DeepSeek-V3](#paper-06) | ☐ | ☐ | ☐ |
| 7 | [Linear Attention](#paper-07) | ☐ | ☐ | ☐ |
| 8 | [Gated DeltaNet](#paper-08) | ☐ | ☐ | ☐ |
| 9 | [DeepSeek-V3.2 / DSA](#paper-09) | ☐ | ☐ | ☐ |
| 10 | [Qwen3.8-Next](#paper-10) | ☐ | ☐ | ☐ |
| 11 | [ViT](#paper-11) | ☐ | ☐ | ☐ |
| 12 | [CLIP](#paper-12) | ☐ | ☐ | ☐ |
| 13 | [SigLIP 2](#paper-13) | ☐ | ☐ | ☐ |
| 14 | [LLaVA](#paper-14) | ☐ | ☐ | ☐ |
| 15 | [Qwen3-VL](#paper-15) | ☐ | ☐ | ☐ |
| 16 | [BLIP-2](#paper-16) | ☐ | ☐ | ☐ |
| 17 | [Kimi Linear](#paper-17) | ☐ | ☐ | ☐ |
| 18 | [Qwen3.5-Omni](#paper-18) | ☐ | ☐ | ☐ |
| 19 | [Gemma 4](#paper-19) | ☐ | ☐ | ☐ |

### 논문마다 남길 1페이지 노트

```text
논문:
읽은 날짜:
이번에 읽은 절:

1. 기존 방법의 문제:
2. 핵심 아이디어 한 문장:
3. 입력 → 중간 표현/상태 → 출력:
4. 핵심 수식 하나와 각 기호의 tensor shape:
5. 학습과 추론에서 다른 점:
6. 줄어드는 비용: 연산 / 캐시 저장 / 메모리 이동 / 기타
7. 남는 비용 또는 성능상 절충:
8. 가장 중요한 ablation 하나:
9. 앞서 읽은 논문과의 연결:
10. 아직 설명하지 못하는 질문:
```

### 작은 확인 과제

- [ ] **Attention:** 길이 4인 시퀀스에서 causal mask를 손으로 그린다.
- [ ] **RoPE:** 2차원 벡터 두 개를 서로 다른 위치만큼 회전해 내적을 비교한다.
- [ ] **KV cache:** layer 수·KV head 수·head dimension·dtype·토큰 수로 저장량을 계산한다.
- [ ] **MoE:** 전체 파라미터와 토큰당 활성 파라미터를 구분해 적는다.
- [ ] **Linear Attention:** 이전 상태에 새 K/V 정보를 반영하는 갱신식을 한 번 계산한다.
- [ ] **VLM:** 이미지 patch부터 projector/merger를 거쳐 LLM까지 tensor shape를 표시한다.
- [ ] **영상:** 프레임 수·해상도·visual token 수가 문맥 예산에 미치는 영향을 정리한다.

처음에는 전체 논문 정독보다 **그림 → 핵심 수식 → 모듈 입출력 → ablation** 순서를 권합니다. 공식 코드 실행은 개념이 잡힌 이후에 붙여도 됩니다.

---

*이 문서는 2026-10-13에 확인한 원논문과 웹 자료를 바탕으로 작성했습니다. 한국어·영어 분류는 자료의 주된 언어이며, 영상의 한국어 자막 제공 여부는 별도로 검증하지 않았습니다. 일부 링크는 같은 자료를 관련 논문에 다시 배치했습니다.*
