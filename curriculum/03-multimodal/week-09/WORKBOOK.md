# 9주 실습 워크북 — ViT로 이미지의 토큰화 이해

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. Patch 순서를 눈으로 확인

우선 RGB 4×4 이미지와 patch2를 사용하는 교육용 예제를 만듭니다. 왼쪽 위·오른쪽 위·왼쪽 아래·오른쪽 아래의 2×2 영역을 각각 red/green/blue/white로 채웁니다. 공간 순서를 row-major로 정했다면 patch 순서는 [red,green,blue,white]입니다.

| 입력 | patch 수 | raw patch 폭 |
|---|---:|---:|
| RGB 4×4, patch2 | 4 | 3×2×2=12 |
| RGB 32×32, patch16 | 4 | 3×16×16=768 |
| RGB 64×64, patch16 | 16 | 768 |

모든 pixel을 0으로 하고 하나만 1로 바꾼 fixture도 만듭니다. 어느 patch의 어느 원소가 변해야 하는지 좌표로 먼저 정합니다. reshape가 실행된다는 것만으로 순서가 맞다고 판단하지 않습니다.

## 2. Encoder를 한 층씩 추가

A. patchify만 → B. projection → C. position embedding → D. vision self-attention → E. MLP/residual.

처음에는 분류용 CLS token과 classifier 없이 patch feature를 유지합니다. vision self-attention에는 같은 이미지 내부의 양방향 mask를 사용합니다. 언어 decoder의 causal mask와 다릅니다.

projection을 비교할 때 실제 구현이 Conv2d인지 Linear인지와 무관하게 flatten/channel 순서를 맞춰야 합니다. 목표 모델의 Conv3d는 12주차에서 연결합니다.

## 3. 두 이미지가 섞이지 않는지 검사

batch에 [red 이미지, blue 이미지]를 넣고, 두 번째만 green으로 바꿉니다. 첫 이미지의 feature는 eval 상태에서 그대로여야 합니다. packed sequence로 합쳤다면 각 이미지의 시작/끝 경계가 attention에 전달되는지 확인합니다.

position을 끈 경우와 켠 경우의 permutation 성질을 구분합니다. position이 켜진 모델에서 patch 순서를 임의로 뒤집고 “결과가 같아야 한다”고 검사하지 않습니다.

## 4. 학습 전에 통과할 것

- 좌표 하나 변경 → 예상 patch 하나만 raw 값 변화.
- 같은 이미지의 단건/배치 실행 → 유효 patch feature 일치.
- raw→projection→position→block 각 단계의 shape 표 작성.
- token 수가 안 맞으면 resizing 여부와 patch 경계부터 확인.

자료는 ViT §3의 patch 식과 README의 한국어/영어 설명을 사용합니다. GPT에는 색 격자와 flatten 순서를 먼저 설명한 뒤 shape 검토를 요청합니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
