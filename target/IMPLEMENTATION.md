# 구현 명세와 완료 기준

[목표 구조](README.md) · [주차 지도](../curriculum/README.md)

이 문서는 **사용자가 직접 수행할 구현 과제의 명세**입니다. 기본 학습 완주와 목표 구조 전체의 축소 통합을 구분합니다. 지금 모델 코드나 통과한 테스트를 제공하는 것이 아닙니다. 작은 reference를 먼저 완성하고, 성능 최적화는 그 뒤에 판단합니다.

## 재현 수준

| 수준 | 이번 과정의 범위 |
|---|---|
| 구조 이해 | 필수: 모든 주 경로의 tensor/state 흐름을 설명 |
| 작은 수치 구현 | 필수: 손계산·불변성·부분 연산 대조. 공식 전체 logits 일치는 심화 |
| 작은 학습 | 필수: synthetic text/image/video에서 gradient와 입력 의존성을 검사 |
| 원본 weight 실행 | 선택: 하드웨어가 충분할 때 별도 수행 |
| 원본 학습 성능·대규모 recipe | 범위 밖: 같은 데이터·연산 예산·훈련 기록이 없음 |
| MTP·Muon·전용 sparse kernel·host offload | 원리는 설명, 실제 구현은 별도 확장 항목 |

MTP를 빼고도 주 모델의 autoregressive 입력→다음 token 경로를 검증할 수 있습니다. 다만 **원본의 모든 학습·추론 기능을 재현했다는 표현은 쓰지 않습니다.** 공개 HF 코드가 MTP weights를 무시하는 점도 보고서에 적습니다.

<a id="tiny"></a>
## 단계별 설정

[첫 dense LM과 목표형 8층 설정](TINY_CONFIG.md)을 분리했습니다. 1~2주는 기본 LM부터 시작합니다. 환경 구성은 [ENVIRONMENT](ENVIRONMENT.md), 부분 연산·가중치 비교는 [PARITY](PARITY.md)를 따릅니다.

## 모듈별 입력·출력 계약

기호: B=batch, T=text+visual sequence 길이, D=text hidden, C=residual branches, N=vision patch 수, Dv=vision hidden. 각 구현은 batch/sequence 축의 실제 순서를 고정해 문서화합니다.

| 모듈 | 입력 → 출력 | 반드시 보존할 것 |
|---|---|---|
| embedding / head | IDs [B,T] → [B,T,D] → logits [B,T,V] | label shift와 special ID |
| GR | [B,T,C,D] → sublayer [B,T,D] → [B,T,C,D] | branch별 read/injection, 최종 collapse |
| GDN | [B,T,D] + state → [B,T,D] + new state | Q/K norm, short conv, decay/write/output gate |
| QSA | [B,T,D] + positions/cache → [B,T,D] | indexer→block mask→GQA→gate |
| MoE | [B,T,D] → [B,T,D] | top-k weight normalization, gated shared expert |
| PLE | original token IDs + branch state → branch update | hash·EOS 경계·dilated conv·주입 위치 |
| Vision | processor patches + grid_thw → [N,Dv] | patch 순서·위치 보간·packed 경계 |
| Merger | [N,Dv] → [N/merge²,D] | 격자별 merge 순서 |
| Assembler | token embeddings + visual features → [B,T,D] | placeholder 수, PLE용 원래 ID 보존 |
| Generator | prefill + 새 token → logits + 여러 cache | 종료·padding·position offset·상태 reset |

## Cache를 하나로 뭉뚱그리지 않기

| 상태 | 소유 모듈 | 길이에 따라 증가? | 경계 검사 |
|---|---|---|---|
| recurrent matrix | GDN | 고정 shape | chunk 분할·초기화 |
| short convolution history | GDN | 고정 최대 길이 | 처음 몇 token |
| K/V | QSA core | 증가 | causal offset·padding |
| indexer key history | QSA indexer | reference에서는 증가 | block 완성 전/후·tail |
| 최근 token ID | n-gram | 고정 최대 길이 | EOS·문서 시작 |
| dilated convolution history | PLE | 고정 최대 길이 | dilation과 prefix 길이 |
| visual features/position delta | multimodal wrapper | 입력 구성에 따름 | prefill 재사용·image count |

## 검증 절차

[PARITY](PARITY.md)의 고정 입력 → 모듈 → 층 → 전체 비교 순서를 사용합니다. 일부 token을 실제로 제외하는 QSA 검사를 포함합니다. 같은 seed만으로 같은 가중치라고 가정하지 않습니다.

## 학습과 성능을 구분하기

합성 텍스트·색상·개수·영상 순서 과제로 학습 경로를 검증합니다. 동일 질문에 서로 다른 시각 정답을 주어 언어 shortcut을 줄이고, 별도 seed의 validation·image shuffle·text-only 대조를 둡니다. 수치 기준은 실험 전에 정합니다. 적은 데이터의 과적합은 기능 검사이며 범용 VLM 성능의 증거가 아닙니다.

구체적인 데이터·label mask·학습 순서는 [14주 워크북](../curriculum/03-multimodal/week-14/WORKBOOK.md)을 따릅니다. QSA indexer는 작은 dense teacher 분포로 warm-up한 후 sparse 경로와 함께 학습하는 과제를 둡니다. 공개 inference class에 학습용 KL loss가 없으면 학습 코드에서 별도로 구성하고 frozen/trainable parameter를 검증합니다. 논문의 대규모 token 수나 step 수를 그대로 복제하지 않습니다.

속도는 **prefill/decode를 분리**하고 batch·길이·dtype·장치·warm-up·반복 수를 기록합니다. GPU는 timing 전에 동기화합니다. 순차 GDN이나 dense mask QSA의 결과로 논문 fused kernel speedup을 주장하지 않습니다.

<a id="done"></a>
## 최종 완료 판정

완료 체크는 16주차에서 하고, 아래 표는 목표 구조 통합 보고서의 목차로 사용합니다. 기본 학습 완주와 전체 통합의 차이는 [16주 워크북](../curriculum/03-multimodal/week-16/WORKBOOK.md)을 따릅니다.

| 보고할 항목 | 필요한 증거 |
|---|---|
| 전체 구조 | 텍스트/이미지/영상 경로와 실제 shape를 적은 그림 |
| 필수 연산 | GDN·QSA·MoE·GR·PLE·vision·merger·MRoPE 구현 파일 |
| 수치 정확성 | 손계산·불변성·부분 연산 대조. official reference는 수행 범위와 미검증 범위를 표시 |
| 생성 | text/image/video의 prefill+cached decode 일치 |
| 학습 | 작은 train/validation과 시각 정보 제거 대조 |
| 범위 | 원본과 다른 크기·vocab·학습·MTP·kernel 등을 명시 |
| 재실행 | 환경 버전·seed·config·fixture·실행 절차 |

MTP/최적화 kernel/원본 성능이 없어도 **주 경로의 축소 reference 구현**으로는 완료할 수 있습니다. GDN이나 QSA, 영상 위치를 일반 attention/임의 embedding으로 대체한 상태라면 해당 구성요소는 미완료로 남깁니다.
