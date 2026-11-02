# 논문별 선택 해설

[필수 논문 목록](README.md) · [학습 지도](../curriculum/README.md)

## 해설 자료를 고르는 방법

아래 20개는 같은 내용을 다른 언어·방식으로 설명하는 **선택 자료**입니다. 한국어 1개로 충분하면 영어까지 모두 읽지 않습니다. 논문의 사실과 목표 모델의 세부 구현은 원문·고정 config·코드에서 확인합니다. 해설은 이해를 돕는 용도입니다.

<a id="e01"></a>
### E01 · Attention Is All You Need

**독해 후 할 수 있어야 하는 것:** 수치 예제에서 logits까지 shape를 설명.

- **한국어 · 그림 해설** — [The Illustrated Transformer 번역](https://nlpinkorean.github.io/illustrated-transformer/): 입문 첫 자료. 전체 구조에서 Q/K/V와 여러 head까지 그림으로 설명.
- **영어 · 그림 해설** — [Jay Alammar — The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/): 한국어 번역의 원문. encoder와 decoder의 데이터 흐름을 설명.
- **영어 · 영상·시각화** — [3Blue1Brown — Attention in transformers](https://www.3blue1brown.com/lessons/attention/): Q/K/V와 attention을 시각적으로 설명. 논문 전체 리뷰보다 개념 입문에 적합.

<a id="e02"></a>
### E02 · RoFormer: Enhanced Transformer with Rotary Position Embedding

**독해 후 할 수 있어야 하는 것:** 상대 위치 내적과 partial RoPE를 구현.

- **한국어 · 논문 리뷰** — [JiYeop Kim — RoFormer](https://kimjy99.github.io/논문리뷰/roformer/): 수식과 위치 인코딩의 요구 조건을 따라 읽기.
- **한국어 · YouTube** — [딥러닝 논문읽기 모임 — RoFormer](https://www.youtube.com/watch?v=tRe2XHF6UbQ): 회전행렬을 이용하는 위치 임베딩 설명. 영상 전체 재생 검증은 하지 않음.
- **영어 · 단계별 유도** — [You could have designed state of the art positional encoding](https://huggingface.co/blog/designing-positional-encoding): 위치 인코딩의 요구 조건에서 시작해 RoPE까지 유도.

<a id="e03"></a>
### E03 · GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints

**독해 후 할 수 있어야 하는 것:** MHA/MQA/GQA 및 cache 크기 계산.

- **한국어 · 논문 리뷰** — [JiYeop Kim — GQA](https://kimjy99.github.io/논문리뷰/gqa/): head 공유와 체크포인트 변환을 논문 흐름대로 설명.
- **영어 · 수식·구현 해설** — [Pratham Patel — Grouped-Query Attention](https://prathamp.com/blog/attention-gqa/): 캐시 크기와 head 그룹 구성을 더 자세히 공부.

<a id="e04"></a>
### E04 · Gated Delta Networks: Improving Mamba2 with Delta Rule

**독해 후 할 수 있어야 하는 것:** 상태를 넘기는 순차 GDN 구현.

- **한국어·영어 · 수식·논문 리뷰** — [Junwon Lee — Gated Delta Networks](https://romaanj.github.io/reviews/gated-deltanet/): 언어 전환을 제공하는 상세 리뷰. fast-weight와 상태 갱신 관점.
- **영어 · 개념 개요** — [Sebastian Raschka — Gated DeltaNet](https://sebastianraschka.com/llms-from-scratch/ch04/08_deltanet/): recurrent memory와 hybrid의 짧은 소개. 단계별 구현 튜토리얼은 아님.

<a id="e05"></a>
### E05 · An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale

**독해 후 할 수 있어야 하는 것:** patch 순서와 시각 feature shape 검증.

- **한국어 · 논문 리뷰** — [CKtrace — ViT 리뷰](https://cktrace.tistory.com/86): Patch embedding과 encoder의 전체 흐름을 설명.
- **한국어 · YouTube** — [텐초 — Vision Transformer 파헤치기](https://www.youtube.com/watch?v=QkwNdgXcfkg): 한국어 논문 리뷰 영상. 검색 메타데이터로 확인.
- **영어 · YouTube** — [Yannic Kilcher — An Image is Worth 16x16 Words](https://www.youtube.com/watch?v=TrdevFK_am4): 영어 논문 설명 영상. 전체 재생과 자막은 미검증.

<a id="e06"></a>
### E06 · Visual Instruction Tuning

**독해 후 할 수 있어야 하는 것:** placeholder와 assistant-only label mask 구현.

- **한국어 · 논문 리뷰** — [JiYeop Kim — Visual Instruction Tuning](https://kimjy99.github.io/논문리뷰/llava/): 구조와 instruction 데이터 생성·학습 단계를 따라 읽기.
- **한국어 · YouTube** — [LLaVA Visual Instruction Tuning 논문 리뷰](https://www.youtube.com/watch?v=jm6He0Bad_o): 한국어 영상으로 구조와 학습 흐름을 복습.
- **영어 · 공식 프로젝트 해설** — [LLaVA project page](https://llava-vl.github.io/): 논문·그림·데모·공식 자료의 시작점.

<a id="e07"></a>
### E07 · Qwen3-VL Technical Report

**독해 후 할 수 있어야 하는 것:** t/h/w 위치와 processor 출력 대조.

- **한국어 · 논문 리뷰** — [moovzi — Qwen3-VL 테크니컬 리포트](https://mvje.tistory.com/310): DeepStack·MRoPE와 학습 전략을 정리.
- **영어 · 공식 구조·사용 개요** — [QwenLM — Qwen3-VL](https://github.com/QwenLM/Qwen3-VL): 공식 architecture 개요와 이미지·영상 입력 예시.

<a id="e08"></a>
### E08 · On the Design of Qwen3.8-Next Architecture: Evaluation, Efficiency, and Training Stability

**독해 후 할 수 있어야 하는 것:** Qwen3.8-Flash-Next의 구조를 축소 구현하고 대조.

- **한국어 · 상세 구조 리뷰** — [dontech — Qwen3.8-Flash-Next 테크 리포트](https://donmain.dev/issue/qwen3-8-flash-next/): 네 가지 아키텍처와 설계 실험을 정리.
- **영어 · 공식 블로그** — [Alibaba Cloud — A New Architecture, Towards Ultimate Cost-Efficiency](https://www.alibabacloud.com/blog/qwen3-8-flash-next-a-new-architecture-towardsultimate-cost-efficiency_603501): 팀의 모델 발표와 구성요소 개요.

