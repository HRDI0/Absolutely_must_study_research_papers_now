# 구조 이해에서 작은 모델 구현까지 — 16주 지도

[홈](../README.md) · [필수 논문 8편](../resources/README.md) · [목표 구조](../target/README.md)

목표는 **Qwen3.8-Flash-Next의 축소 구조 구현**입니다. 다른 최신 모델 비교와 71편 완독은 이번 과정에서 제외합니다. 논문을 읽은 뒤 매번 새 모델로 이동하지 않고 같은 구현에 모듈을 붙입니다.

먼저 [시작 전 진단](START.md)을 진행합니다. 2주 기본 LM → 4주 모듈 확장 → 8주 텍스트 본체 → 10~14주 시각 연결과 작은 학습 순서입니다. 15~16주는 수행한 비교와 미검증 범위를 정리합니다.

## 3개 단원

| 단원 | 기간 | 완성할 것 |
|---|---|---|
| 1. 기초 연산과 작은 언어모델 | 1~4주 | causal decoder + RoPE/GQA + MoE |
| 2. Qwen 텍스트 본체 | 5~8주 | GDN/QSA hybrid + GR + n-gram |
| 3. 이미지·영상과 통합 검증 | 9~16주 | vision/merger/위치/cache + 학습·reference 검증 |

## 주차별 시작점

| 주 | 난이도 | 내용 | 필독 | 산출물 |
|---|---|---|---|---|
| 1 | 기초 | [Transformer를 텐서 연산으로 읽기](01-basics/week-01/README.md) | E01 | 텐서 shape 표 + 3토큰 수치 예제 + causal attention 모듈 |
| 2 | 기초 | [Annotated Transformer에서 작은 언어모델로](01-basics/week-02/README.md) | E01 | Annotated 코드 독해 노트 + 작은 LM의 loss 곡선 |
| 3 | 기초 | [RoPE·GQA·KV cache](01-basics/week-03/README.md) | E02, E03 | RoPE/GQA/KV 모듈 + cache 메모리 계산표 |
| 4 | 중급 | [SwiGLU·MoE와 목표 모델 설정 읽기](01-basics/week-04/README.md) | 새 논문 없음 | MoE 모듈 + 축소 설정 명세 + 활성/전체 파라미터 비교 |
| 5 | 중급 | [Gated DeltaNet의 상태 갱신](02-backbone/week-05/README.md) | E04 | 순차 GDN reference + 상태 이동 그림 |
| 6 | 심화 | [Qwen식 GDN과 Gated Residual](02-backbone/week-06/README.md) | E08 | Qwen GDN·GR 모듈 + 원형 대비 변경표 |
| 7 | 심화 | [QSA의 indexer와 sparse core attention](02-backbone/week-07/README.md) | E08 | QSA indexer + sparse mask 기준 구현 + 경계값 테스트표 |
| 8 | 심화 | [N-gram embedding과 텍스트 본체 조립](02-backbone/week-08/README.md) | E08 | 완성된 tiny text backbone + 구성요소별 parity 기록 |
| 9 | 중급 | [ViT로 이미지의 토큰화 이해](03-multimodal/week-09/README.md) | E05 | patchify/ViT 모듈 + 이미지 좌표-토큰 대응표 |
| 10 | 중급 | [LLaVA로 시각·언어 연결과 SFT 이해](03-multimodal/week-10/README.md) | E06 | 연결부 학습 실험 + label mask 표 |
| 11 | 심화 | [Qwen식 이미지·영상 위치와 processor](03-multimodal/week-11/README.md) | E07 | 멀티모달 입력 contract + 4개 processor fixture |
| 12 | 심화 | [목표 Vision encoder·merger·영상 경로 구현](03-multimodal/week-12/README.md) | 새 논문 없음 | tiny multimodal forward + vision/merger parity 표 |
| 13 | 심화 | [Hybrid cache와 멀티모달 생성](03-multimodal/week-13/README.md) | 새 논문 없음 | hybrid cache 명세 + text/image/video generation 비교 |
| 14 | 심화 | [작은 학습으로 모델이 정보를 사용하는지 검증](03-multimodal/week-14/README.md) | E06, E08 | 소규모 학습 보고서 + leakage 점검 + 시각/영상 ablation |
| 15 | 심화 | [공식 구현 대조와 시간·메모리 측정](03-multimodal/week-15/README.md) | 새 논문 없음 | parity 결과표 + prefill/decode 메모리·시간 표 |
| 16 | 심화 | [한 모델의 재현 범위 확정과 최종 설명](03-multimodal/week-16/README.md) | E08 | 최종 구조도 + 재현 보고서 + 재실행 안내 |

E 번호는 [8편 목록](../resources/README.md)과 같습니다. **E08은 하나의 논문을 여러 주에 나눠 읽는 것**입니다. MoE·GR·n-gram·cache는 별도 논문을 늘리지 않고 목표 구현과 해당 절에서 배웁니다.

## 진행 방법

현재 주차 README의 자료 → 같은 폴더 WORKBOOK의 손계산·단계별 구현·검사 → README의 완료 체크 → 다음 주 순서로 진행합니다. [PROGRESS](../PROGRESS.md)에는 현재 위치만 적습니다. 별도 capstone·체크리스트·논문 노트 사이를 반복해서 이동할 필요가 없습니다.

이전 기준인 주 36시간으로 배분하면 16주 총 576시간이지만, 실습이 오래 걸리면 해당 주를 연장합니다. 미분·행렬곱·tensor reshape가 낯설다면 1~2주를 먼저 충분히 반복합니다. 전체 모델을 한꺼번에 설치하거나 대규모 weight를 내려받는 것으로 시작하지 않습니다.

긴 노트는 [작성 규칙](WRITING.md)에 따라 같은 주차 폴더 안에서 나눕니다. 공식 전체 모델 수치 일치는 심화 목표이며, 기본 완료에는 손계산·불변성·부분 연산 검사와 작은 학습 증거를 남깁니다.

모든 구현은 앞으로 수행할 학습 과제입니다. 이 문서를 작성하면서 모델 구현·학습·성능 재현을 실행한 것은 아닙니다.
