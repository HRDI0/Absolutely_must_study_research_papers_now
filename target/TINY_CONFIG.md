# 축소 설정 — 첫 실습과 최종 설계 구분

[구현 기준](IMPLEMENTATION.md) · [환경 준비](ENVIRONMENT.md)

## 먼저 만드는 기본 LM

1~2주는 vocab=16, hidden=32, heads=2, dense FFN 폭=64, layers=1, 길이=8~16, batch=1, dropout=0의 **교육용 시작안**을 사용합니다. BOS=0, EOS=1, PAD=2, 일반 문자=3~15로 둡니다. 일반 residual·LayerNorm·고정 위치 encoding에서 시작하고, RoPE/GQA·MoE는 이후 별도로 추가합니다. 아래 8층 전체 설정은 1주차부터 만들 대상이 아닙니다.

학습 입력 예시는 2주차 워크북을 따릅니다. 작게 시작해도 변수의 뜻과 shape를 먼저 검사합니다. 기본 LM과 아래 목표형 설정은 모두 이번 작업에서 실행 검증한 결과가 아닙니다.

<a id="tiny"></a>
## 목표 구조를 조립할 때의 8층 설정

아래는 **실행 확인 전의 교육용 설계안**입니다. checkpoint의 작은 공식 변종이 아닙니다. 생성자 검사와 작은 forward를 통과한 뒤 실제 사용 config를 저장합니다. 공식 checkpoint config를 그대로 생성하면 거대한 embedding/expert가 할당될 수 있으므로 먼저 작은 값으로 구성합니다.

| 항목 | 시작값 | 유지/변경 근거 |
|---|---|---|
| text hidden / layers | 128 / 8 | hybrid cycle 2회 |
| layer 순서 | G,G,G,Q,G,G,G,Q | 3:1 비율 유지 |
| vocabulary | 256 | 합성 과제 전용. 공식 tokenizer와 혼용 금지 |
| BOS/EOS/PAD/image/video/start/end | 각각 240/241/242/243/244/245/246 | 0~255 내 서로 다른 예약 ID 예시 |
| QSA Q / KV heads / head dim | 12 / 1 / 32 | 12:1 sharing 유지, projection 폭은 hidden과 달라도 됨 |
| Indexer Q / KV heads / dim | 4 / 1 / 32 | MQA 유지 |
| block / token budget | 4 / 16 | 예산은 block 크기의 배수 |
| GDN key heads / value heads / head dims | 1 / 3 / 32 | value:key=3 유지 |
| GDN conv | 4 | local context 유지 |
| experts / active / shared | 8 / 2 / 1 | routing 동작 유지, 원본 비율과 파라미터 수는 축소 |
| expert intermediate | 64 | routed/shared 모두 작은 폭 |
| residual streams / rank | 4 / 16 | branch 수 유지, projection rank 축소 |
| ngram size / heads per ngram | 3 / 8 | bigram/trigram과 다중 hash 유지 |
| ngram base vocab / PLE dim | 257 / 128 | 큰 lookup 축소, dim은 16개 head로 나누어짐 |
| PLE layer / conv | [2] / 4 | 코드가 1-based 층 번호로 비교, dilation 규칙 유지 |
| rotary factor / sections | 0.25 / [2,1,1] | head32의 rotary8, section 합4 |
| vision hidden / depth / heads | 64 / 2 / 4 | vision 본체를 작게 구성 |
| vision intermediate / learned positions | 128 / 16 | 4×4 위치 격자 시작안 |
| patch / temporal patch / spatial merge | 16 / 2 / 2 | 목표 patch·merge 규칙 유지 |
| vision output hidden | 128 | text hidden과 일치 |
| sequence / batch / dtype | 32~256 / 1 / FP32 | CPU correctness 실험부터 시작 |

기타 epsilon, activation, seed, gate, norm_topk_prob 등의 값은 [config 클래스](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/configuration_qwen4_exp.py)와 checkpoint에서 읽고 명시적으로 기록합니다. 표의 축소 설정을 복사해도 자동으로 완성된 모델이 되는 것은 아닙니다. 특히 processor가 만드는 공식 token ID를 쓰려면 text vocabulary/embedding과 예약 ID를 공식 값으로 맞추거나, **합성 tokenizer fixture를 별도로 작성**해야 합니다. 두 경우를 같은 parity 실험에 섞지 않습니다.

