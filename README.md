# Absolutely_must_study_research_papers_now

현대 **LLM·VLM의 내부 구조**를 이해하기 위한 논문 공부 저장소입니다. Transformer의 Q/K/V부터 KV cache, MoE, 선형·희소 attention, 이미지·영상·음성 연결까지 순서대로 공부합니다.

- **기본 12주:** 주 학습 논문 15편 + 마지막 주 통합 프로젝트
- **선택 심화 4주:** BLIP-2, Kimi Linear, Qwen3.5-Omni, Gemma 4
- **권장 시간:** 주당 6~8시간. 수식이 처음이거나 실습을 깊게 하면 각 주를 2주로 나눕니다.
- **자료:** 논문별 한국어·영어 해설/영상/구현 4~5개, 총 79개 항목(78개 고유 URL)
- **자료 조사 기준일:** 2026-10-13. 공개일·검증 범위는 [원본 자료집](resources/reading-list.md)에 기록했습니다.
- **저장소:** [HRDI0/Absolutely_must_study_research_papers_now](https://github.com/HRDI0/Absolutely_must_study_research_papers_now)

## 바로 시작하기

1. [전체 커리큘럼](curriculum/README.md)에서 학습 경로와 시간 배분을 확인합니다.
2. [1주차](curriculum/week-01/README.md)의 읽을 범위와 과제를 봅니다.
3. 논문별 `README.md`에서 해설을 고르고 같은 폴더의 `NOTES.md`에 공부 내용을 작성합니다.
4. 그 주차의 README 아래에 회고를 남기고 [진행표](PROGRESS.md)를 업데이트합니다.
5. [12주차 최종 프로젝트](capstone/README.md)에서 구조 비교 보고서를 완성합니다.

처음에는 **한국어 해설 → 원논문 그림·핵심 수식 → 영어 해설 → 직접 설명/작은 실습** 순으로 읽습니다. 해설을 모두 볼 필요는 없습니다.

## 주차별 학습표

| 주차 | 주제 | 논문 | 결과물 |
|---|---|---|---|
| [1주](curriculum/week-01/README.md) | Transformer의 데이터 흐름 | [Transformer](papers/01-foundations/01-transformer/README.md) | 블록 그림 1장 + attention shape 표 |
| [2주](curriculum/week-02/README.md) | 현대 decoder 블록과 위치 정보 | [RoFormer / RoPE](papers/01-foundations/02-roformer/README.md), [LLaMA](papers/01-foundations/03-llama/README.md) | RoPE 유도 노트 + 블록 비교 그림 |
| [3주](curriculum/week-03/README.md) | KV cache와 GPU 메모리 | [GQA](papers/01-foundations/04-gqa/README.md), [FlashAttention](papers/01-foundations/05-flashattention/README.md) | 캐시 계산표 + HBM/SRAM 흐름 그림 |
| [4주](curriculum/week-04/README.md) | MLA와 sparse MoE | [DeepSeek-V3](papers/02-efficient-llms/06-deepseek-v3/README.md) | DeepSeek-V3 구조도 + 비용 비교표 |
| [5주](curriculum/week-05/README.md) | Linear Attention과 recurrent state | [Linear Attention](papers/02-efficient-llms/07-linear-attention/README.md) | 상태 갱신 손계산 또는 작은 실습 노트 |
| [6주](curriculum/week-06/README.md) | Gated DeltaNet의 기억 갱신 | [Gated DeltaNet](papers/02-efficient-llms/08-gated-deltanet/README.md) | 세 가지 state update 비교표 |
| [7주](curriculum/week-07/README.md) | Sparse와 hybrid 구조 비교 | [DeepSeek-V3.2 / DSA](papers/02-efficient-llms/09-deepseek-v3-2/README.md), [Qwen3.8-Next](papers/02-efficient-llms/10-qwen3-8-next/README.md) | Sparse/hybrid 비교표 + ablation 해석 1개 |
| [8주](curriculum/week-08/README.md) | 이미지를 토큰으로 바꾸기 | [ViT](papers/03-vision-language/11-vit/README.md) | 이미지→patch→embedding→encoder 그림 |
| [9주](curriculum/week-09/README.md) | 시각 표현의 정렬과 학습 | [CLIP](papers/03-vision-language/12-clip/README.md), [SigLIP 2](papers/03-vision-language/13-siglip2/README.md) | Loss 비교표 + global/patch feature 메모 |
| [10주](curriculum/week-10/README.md) | Vision encoder와 LLM 연결 | [LLaVA](papers/03-vision-language/14-llava/README.md) | LLaVA 연결도 + 학습 단계표 |
| [11주](curriculum/week-11/README.md) | 이미지·영상 VLM의 전체 구조 | [Qwen3-VL](papers/03-vision-language/15-qwen3-vl/README.md) | Qwen3-VL 구조도 + 영상 token 예산표 |
| [12주](curriculum/week-12/README.md) | 통합 복습과 최종 결과물 | 앞선 15편 통합 복습 | 최종 비교 보고서 + 구조도 3장 + 발표 개요 |
| [13주 · 선택](curriculum/week-13/README.md) | Projector와 Q-Former | [BLIP-2](papers/04-advanced/16-blip2/README.md) | Connector 비교표 |
| [14주 · 선택](curriculum/week-14/README.md) | KDA와 hybrid attention | [Kimi Linear](papers/04-advanced/17-kimi-linear/README.md) | GDN/KDA 비교 노트 |
| [15주 · 선택](curriculum/week-15/README.md) | 음성·영상과 스트리밍 | [Qwen3.5-Omni](papers/04-advanced/18-qwen3-5-omni/README.md) | 시청각 입출력 흐름도 |
| [16주 · 선택](curriculum/week-16/README.md) | Encoder-free 비교 | [Gemma 4](papers/04-advanced/19-gemma4/README.md) | 입력 구조 비교 보고서 |

## 폴더 구조

```text
.
├── README.md                  # 시작점과 학습표
├── PROGRESS.md                # 주차·논문 진행 현황
├── UPLOAD.md                  # 지정 GitHub 저장소에 올리는 방법
├── curriculum/
│   ├── README.md              # 시간 배분·선수 지식·완료 규칙
│   └── week-01/ ... week-16/  # 주차별 계획·실습·회고
├── papers/
│   ├── README.md              # 19편 전체 목록
│   ├── 01-foundations/        # Transformer, RoPE, LLaMA, GQA, FlashAttention
│   ├── 02-efficient-llms/     # MLA, MoE, linear/sparse/hybrid attention
│   ├── 03-vision-language/    # ViT, CLIP, SigLIP 2, LLaVA, Qwen3-VL
│   └── 04-advanced/           # BLIP-2, Kimi Linear, Omni, Gemma 4
│       └── <논문 폴더>/
│           ├── README.md      # 원문·해설·읽을 범위·완료 질문
│           └── NOTES.md       # 직접 채우는 학습 노트
├── resources/
│   └── reading-list.md        # 기존 전체 자료집·검증·개념 비교
├── exercises/
│   └── README.md              # 실습 원칙·저장 방법·확인 질문
├── capstone/
│   ├── README.md              # 최종 프로젝트 지침·평가 기준
│   └── REPORT.md              # 최종 보고서 작성 양식
├── templates/
│   ├── paper-note.md
│   ├── experiment.md
│   └── weekly-review.md
└── assets/
    └── README.md              # 직접 만든 그림·표 저장 규칙
```

각 논문 폴더에는 README와 NOTES가 있습니다. 폴더명은 링크·도구 호환성을 위해 영어로, 학습 안내는 한국어로 작성했습니다.

## 완료의 기준

“읽음”과 “설명 가능”을 따로 기록합니다. 다음을 만족하면 논문 하나를 완료로 표시합니다.

- 핵심 문제와 아이디어를 3문장 이내로 설명
- 입력 → 중간 표현/상태 → 출력과 중요한 tensor shape 표시
- 핵심 수식 또는 작은 예제 1개 직접 풀이
- 계산량·캐시·메모리 이동 중 무엇이 바뀌는지 구분
- ablation이나 비교 실험 1개를 조건과 함께 해석

주차별 상세 완료 기준은 각 주차 문서에 있습니다. 학습 상태와 결과는 모두 **미시작**으로 준비되어 있습니다.

## 목적에 따른 조정

- **이미지·영상 VLM 우선:** 1 \~ 3주 → 8 \~ 11주 → 4 \~ 7주 → 12주
- **긴 문맥·추론 효율 우선:** 1 \~ 7주 → 선택 14주 → 8 \~ 12주
- **음성·영상 대화 시스템:** 기본 과정 이후 선택 15주를 필수로 추가
- **시간이 부족할 때:** 주차별 핵심 절과 결과물에 집중하고, 전체 논문 정독·대형 모델 실행은 뒤로 미룹니다.

## 자료와 기록 관리

원논문·해설은 링크로 연결합니다. 외부 자료의 권리는 각 저작자에게 있으며, 논문 PDF·블로그 전문을 저장소에 재배포하지 않습니다. 인용한 수식·그림·성능 수치에는 원문과 위치를 남깁니다. AI 요약과 영상의 확인 범위는 논문별 자료표에 표시했습니다.

최신 성능 순위보다 구조를 이해하는 과정입니다. 모델 변형과 실험 조건을 구분하고, 수정할 내용은 출처와 함께 학습 노트에 남깁니다.
