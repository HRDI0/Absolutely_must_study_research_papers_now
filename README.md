# Absolutely_must_study_research_papers_now

**Qwen3.8-Flash-Next 하나의 구조를 이해하고, 작은 크기로 직접 구현하는 학습 과정입니다.**

[시작 전 진단](curriculum/START.md) · [1주차 시작](curriculum/01-basics/week-01/README.md) · [16주 학습 순서](curriculum/README.md) · [필수 논문 8편](resources/README.md) · [이어서 공부하기](PROGRESS.md)

## 목표를 하나로 줄였습니다

기존 자료는 아키텍처·서빙·강화학습·음성·여러 최신 모델을 한꺼번에 다루어, 무엇을 구현하기 위해 읽는지 흐려졌습니다. 이번에는 **필수 논문 8편과 목표 모델의 공개 코드·설정**을 중심으로 다시 구성했습니다. 논문 전체를 모두 읽는 과정도 아닙니다. 각 주차에서 필요한 절을 지정합니다.

목표 모델은 텍스트·이미지·영상을 입력받아 텍스트를 생성하는 **Qwen3.8-Flash-Next**입니다. [모델 카드](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/README.md), [설정](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/config.json), [공개 구현](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py)을 서로 대조할 수 있어 선정했습니다. 음성 인식·음성 생성 모델까지 이번 목표에 포함하지 않습니다.

## 한 줄 학습 경로

**작은 Transformer → RoPE/GQA·MoE → GDN/QSA·GR·n-gram → Vision encoder·merger·위치 정보 → 멀티모달 생성과 검증**

| 큰 단원 | 주차 | 완성하는 부분 |
|---|---|---|
| 1. 기초 연산과 작은 언어모델 | 1~4주 | decoder·KV cache·head 공유·expert routing |
| 2. 목표 모델의 텍스트 본체 | 5~8주 | 3 GDN : 1 QSA hybrid·4 residual branches·n-gram |
| 3. 이미지·영상과 통합 검증 | 9~16주 | 시각 토큰·연결부·위치·cache·작은 학습 실험 |

**각 주차의 README에서 읽을 범위를 확인하고, 같은 폴더의 WORKBOOK에서 작은 입력·예상 결과·구현 단계·실패 원인을 따라갑니다.** 완료 체크는 주차 README 한 곳에서만 합니다. 기존 해설 20개와 시작 전 기초 자료는 선택해서 참고합니다.

## 직접 구현한다는 뜻

사용자가 직접 **작은 학습용 모델**을 구현합니다. 먼저 기본 LM과 개별 연산·작은 시각 학습을 완성하고, 목표 구조 통합과 공식 구현의 수치 대조로 확장합니다. 전체 모델 대조를 기본 실습의 시작 조건으로 두지 않습니다. 원본 규모의 학습·성능 복제는 목표에 포함하지 않습니다.

- [목표 구조와 공식 코드 지도](target/README.md): 실제 config와 구현 심벌을 연결합니다.
- [구현 범위·축소 설정·완료 기준](target/IMPLEMENTATION.md): 어떤 연산을 유지하고 무엇을 축소하는지 명시합니다.
- [보강 내용과 남은 검증](target/REVIEW_RESOLUTION.md): 검수의 8개 지적과 보강 문서를 연결합니다.
- [출처와 확인 범위](target/VERIFICATION.md): 고정 revision, MTP 등 공개 구현의 한계를 기록합니다.

이 저장소에는 현재 **학습 문서와 과제 명세**가 있습니다. 실행 가능한 tiny 모델이나 완료된 실험 결과가 들어 있다는 뜻은 아닙니다. 주 36시간 기준 16주 배분이며, 시간이 더 필요하면 해당 주차를 연장합니다.

## 폴더

```text
curriculum/   # 3개 단원 아래 16개 주차: 자료·실습·체크·노트
resources/    # 필수 논문 8편과 선택 해설
target/      # 목표 구조·구현 명세·검증 출처
archive/      # 이전 71편 자료와 16·40·12단원·30주 기록
```

[이전 자료와 학습 기록](archive/README.md)은 보관했습니다. 현재 과정의 필수 독서 목록에 합산하지 않습니다.

문서는 **120줄 또는 6,000자를 넘기기 전에** 주제별로 분리합니다. 학습 노트도 같은 기준으로 `NOTES-01.md`, `NOTES-02.md`에 이어 씁니다. [문서 작성·분리 규칙](curriculum/WRITING.md)을 따릅니다.

선정·구조 확인일: **2026-11-02**.
