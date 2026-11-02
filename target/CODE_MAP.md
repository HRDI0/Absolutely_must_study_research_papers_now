# 공식 코드 독해 지도

[목표 구조](README.md) · [연산 비교 방법](PARITY.md)

## 공식 코드 지도

| 읽는 순서 | 심벌 | 질문 | 주차 |
|---|---|---|---|
| 1 | [Qwen4ExpForConditionalGeneration](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2397) | 입력부터 logits/loss까지 어떤 모델을 호출하는가? | 4·16 |
| 2 | [Qwen4ExpTextTopKRouter](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L952) · [Qwen4ExpTextSparseMoeBlock](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L973) | top-k와 shared output은 어떻게 합치는가? | 4 |
| 3 | [Qwen4ExpTextGatedDeltaNet](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L456) | 원래 GDN에서 어떤 gate/norm이 달라졌는가? | 5~6 |
| 4 | [Qwen4ExpTextGatedResidual](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L995) · [Qwen4ExpTextDecoderLayer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1252) | stream을 언제 읽고 어디에 쓰는가? | 6 |
| 5 | [Qwen4ExpTextQSAIndexer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L665) · [Qwen4ExpTextAttention](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L811) | 위치 선택과 attention 출력은 어느 경로인가? | 7 |
| 6 | [Qwen4ExpTextNGramEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1072) · [Qwen4ExpTextPLELayer](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1177) | hash/EOS/conv history는 어떻게 유지하는가? | 8 |
| 7 | [Qwen4ExpTextModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1371) | stream 확장·collapse와 PLE ID는 어디서 처리하는가? | 8 |
| 8 | [Qwen4ExpVisionPatchEmbed](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1759) · [Qwen4ExpVisionModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1923) | 이미지/영상 patch와 position은 어떤 shape인가? | 9·12 |
| 9 | [Qwen4ExpVisionPatchMerger](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L1779) · [Qwen4ExpModel](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L2010) | 시각 feature와 placeholder 수는 어떻게 맞추는가? | 10~12 |
| 10 | [Qwen4ExpTextRotaryEmbedding](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py#L76) · [Qwen3VLProcessor](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen3_vl/processing_qwen3_vl.py) | t/h/w 및 video timestamp는 누가 만드는가? | 11 |


GDN은 class 전체에 앞서 같은 파일의 `torch_recurrent_gated_delta_rule` 함수를 읽습니다. QSA는 indexer의 선택 집합과 core의 출력을 따로 검사합니다. 모델 코드를 수정하거나 그대로 복사할 필요는 없습니다.
