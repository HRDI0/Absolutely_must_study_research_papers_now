# 12주 실습 워크북 — 목표 Vision encoder·merger·영상 경로 구현

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 처음부터 영상 전체를 붙이지 않기

Conv3d patch 연산 → vision position/한 block → merger → 단일 이미지 삽입 → 두 이미지 → 영상 순서로 진행합니다. 목표 class는 [공식 코드 지도](../../../target/CODE_MAP.md)를 따릅니다.

## 2. Temporal patch 손계산

C=1,T=2,H=W=2, kernel=[2,2,2], stride도 같고 모든 kernel weight=1,bias=0인 **교육용 Conv3d**를 생각합니다. 입력이 모두 1이면 출력 token 한 개의 값은 8입니다. 한 pixel을 1에서 3으로 바꾸면 출력은 10입니다.

이는 실제 RGB/patch16 설정을 검증하기 전 축과 kernel 범위를 확인하는 예제입니다. 이후 C=3,temporal2,patch16이면 raw patch당 원소 수는 3×2×16×16=1536입니다.

## 3. Merger와 placeholder 개수

| 입력(전처리 뒤 크기 기준) | grid_thw | patch 수 | merge2 뒤 token |
|---|---|---:|---:|
| 같은 frame2개인 64×64 정지 이미지 | [1,4,4] | 16 | 4 |
| 4프레임 64×64 영상 | [2,4,4] | 32 | 8 |

각 spatial 2×2 묶음의 순서를 원문과 맞춥니다. 각 patch가 식별 가능한 색/좌표 값을 갖도록 만들어 묶임을 확인합니다. shape만 맞고 순서가 다른 경우를 반드시 잡아야 합니다.

## 4. 전체 흐름 검사

1. image embedding 삽입 전후의 token embedding을 비교합니다. image 위치만 치환되고 text 위치는 같아야 합니다.
2. PLE가 필요로 하는 원래 token IDs는 별도로 보존합니다.
3. vision encoder를 배치로 실행한 결과와 단건 실행을 비교합니다.
4. 두 영상의 frame이 packed attention 경계를 넘어 섞이지 않는지 확인합니다.
5. image/vision position의 보간 규칙과 decoder MRoPE를 구분합니다. 서로 다른 positional encoding입니다.

영상 timestamp는 frame 순서와 시간의 입력 표현입니다. 무작정 frame token을 concat하는 것과 동일하다고 가정하지 않습니다. 합성 실습에서 생략한 timestamp 규칙은 원본 대비 차이로 남깁니다.

## 5. 실패 원인 찾아가기

| 증상 | 확인 순서 |
|---|---|
| token 수가 4배 차이 | merge 전/후 구분 |
| token 수가 2배 차이 | temporal patch와 frame padding |
| 색/위치 과제가 뒤집힘 | RGB channel·patch 순서·merge 순서 |
| text-only는 되는데 이미지 실패 | special ID·feature 수·dtype |
| 공식 vision 출력만 다름 | 동일 weight·보간·norm·position·backend |

처음에는 pixel feature 경로부터 수치로 대조하고, pretrained 의미 이해를 기대하지 않습니다. 생성과 cache는 다음 주에 붙입니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
