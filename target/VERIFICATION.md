# 선정·검증 기록

[홈](../README.md) · [목표 구조](README.md)

확인일: **2026-11-02**. 본 기록은 자료와 공개 코드의 확인 범위입니다. 학습자의 향후 구현 테스트 결과와 구분합니다.

## 출처 고정

| 출처 | 확인한 버전/경로 | 확인한 내용 |
|---|---|---|
| 기술 보고서 | [arXiv 2608.30320 v1](https://arxiv.org/html/2608.30320v1) | GDN/QSA/GR/ngram, 최적화·학습 설계의 목차·수식 |
| 모델 repository | revision `de4b8e4d43b917e7706784d8bb445c9af86a3540` | [모델 카드](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/README.md), [config](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/config.json), 이미지/영상 processor JSON |
| Transformers | commit `98110a1f5332a9ac5629ccc2aeb6412393df15e9` | [modeling](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py), [configuration](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen4_exp/configuration_qwen4_exp.py), [processor](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/src/transformers/models/qwen3_vl/processing_qwen3_vl.py) |
| 선수 논문 | E01~E07 arXiv 원문 페이지 | 제목·ID·내용 범위와 목표 연결성 |
| Annotated Transformer | [Harvard 원문](https://nlp.seas.harvard.edu/annotated-transformer/) | 코드·목차·학습/생성 예제. 예제 환경 설치·실행은 하지 않음 |

모델 metadata의 숫자만으로 판단하지 않고 config와 구현의 시각 경로를 확인했습니다. checkpoint는 `language_model_only=false`이며 `vision_config`를 포함합니다. processor는 `Qwen3VLProcessor`를 사용합니다.

## 확인된 차이와 제약

- 목표 config의 `deepstack_visual_indexes=[]`를 확인했습니다. Qwen3-VL 논문의 DeepStack을 이번 목표에 필수라고 복사하지 않았습니다.
- `ple_layer_ids=[2]`는 DecoderLayer에서 `layer_idx+1`과 비교합니다.
- QSA indexer는 MQA, core는 GQA 구조입니다. HF reference는 sparse 선택을 mask로 만든 후 attention backend에 전달합니다. sparse kernel의 실측 가속과 동일하지 않습니다.
- config에 MTP 1층이 있지만, 확인한 modeling 파일은 `^mtp.*` 키를 load-ignore 대상으로 둡니다. 이 코드만으로 MTP training/inference를 완료했다고 할 수 없습니다.
- 논문은 block budget을 ceil로 표현하고 reference는 정수 나눗셈을 사용합니다. 교육 설정에서는 budget을 compression ratio의 배수로 제한합니다. indexer score scaling 등 세부 차이도 구현 시 기록합니다.
- pretrained vision/LM의 품질은 구조 설명만으로 재현되지 않습니다. 합성 데이터 실험은 입력 경로와 학습 가능성을 검사합니다.
- 원본 weight의 사용·배포 조건은 checkpoint의 LICENSE를 별도로 따릅니다. 본 문서는 라이선스 법률 검토가 아닙니다.

## 한국어·영어 보조 자료의 확인 수준

20개 해설 항목은 이전에 조사한 관련 자료에서 선별했습니다. 이번에는 한국어 Transformer 번역, RoFormer/GQA/LLaVA 리뷰, GDN 리뷰, ViT 리뷰, Qwen3-VL 리뷰, Qwen3.8-Flash-Next 상세 글과 영어 3Blue1Brown 페이지를 다시 열어 주제와 본문을 확인했습니다. Harvard와 LLaVA 공식 프로젝트도 다시 확인했습니다.

나머지 자료는 기존 조사 기록을 유지했으며 이번에 모두 재열람했다고 표시하지 않습니다. YouTube 링크는 기존 영상 메타데이터 확인 수준입니다. 전체 재생·자막·강의 정확성을 검증하지 않았습니다. 동일 개념의 대체 해설로 제공하며 원문/공식 코드의 근거를 대신하지 않습니다.

## 재구성 원칙

- 필수 논문: 8편. E08을 여러 주에 읽어도 한 편으로 계산합니다.
- 필수 구현 연결: GQA/RoPE, MoE, GDN, QSA, GR, PLE/ngram, vision, merger, 이미지/영상 위치, hybrid cache, CE/SFT/indexer 학습.
- 기존 71편 목록·19개 논문 노트·30주 계획은 보관합니다. 완료 상태는 새 과정으로 자동 이전하지 않습니다.
- 새 과정은 3단원·16주이며, 각 주차 안에 읽기·실습·검증·체크·기록을 모읍니다.

## 이번 작업에서 하지 않은 검증

원본 weight 다운로드, tiny 모델 생성/학습, GPU 메모리·속도 측정, official reference 수치 일치는 수행하지 않았습니다. 축소 config는 설계안이며 실행 성공을 보증하는 배포 artifact가 아닙니다. 이 검증들은 주차별 학습 과제로 명시되어 있습니다.

## 이전 문서 재구성 검사 결과

- 당시 학습 문서 23개, 주차 문서 16개, 필수 논문 ID 8개를 확인했습니다.
- 보관본 포함 Markdown 201개에서 상대 파일 링크·앵커 2,390개를 검사했고 누락을 발견하지 않았습니다.
- 작업 시작 당시 Markdown 177개의 내용을 보관본과 대조했습니다. 이동용 링크와 보관 안내를 제외한 본문·체크·기록 변경은 없었습니다. 논문별 NOTES 19개도 보존했습니다.
- 모델 코드 링크는 확인한 commit에 고정했고, 구성요소별 class 시작 행과 연결했습니다. 모델 실행 검증과 문서 검사는 구분합니다.

## 이번 보강의 확인 범위

[보강 대응표](REVIEW_RESOLUTION.md)에 8개 지적과 수정 위치를 연결했습니다. 16개 주차 워크북, 선수 진단, 환경·대조 절차를 추가하고 긴 자료·설정·검수 문서를 나눴습니다.

- 최종 Markdown 231개(활성 53개·보관 178개)의 내부 파일/앵커 링크 2,624개를 검사했고 누락은 없었습니다.
- 활성 문서의 최댓값은 96줄·4,292자이며 모든 문서가 120줄·6,000자 이내입니다. 보관본 178개와 기존 주차 체크·개인 기록은 보강 시작 시점과 동일하게 보존했습니다.
- 모델 구현·학습은 수행하지 않았습니다. 원본 weight를 내려받거나 reference를 설치하지도 않았습니다.
- 설치 안내와 loss 계약은 PyTorch·Transformers 공식 문서, 고정 commit의 setup.py와 대조했습니다.
- 워크북의 Attention·RMSNorm·MoE·GDN·hash·projection·CE·KL 예제는 별도 산술 계산으로 확인했습니다. 이는 모델 실행 테스트가 아닙니다.
- 문서 길이 제한은 [작성 규칙](../curriculum/WRITING.md)에 따르며, 보관본에는 소급 적용하지 않습니다.
