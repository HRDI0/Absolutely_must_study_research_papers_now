# 11주 실습 워크북 — Qwen식 이미지·영상 위치와 processor

[주차 목표·자료](README.md) → 이 워크북 → [완료 체크](README.md#checklist)

정답 구현 코드는 직접 작성합니다. 먼저 종이에 계산하고 아래 예상 결과와 비교합니다. 수치 예제는 교육용으로 지정한 입력이며, 모델 학습 성공을 보장하는 실측 결과가 아닙니다.

## 1. 두 경로를 명시적으로 분리

| 항목 | A. 내가 만드는 합성 실습 | B. 공식 processor 관찰 |
|---|---|---|
| 사전 | 작은 vocab와 내가 정한 special ID | 공식 tokenizer의 사전/ID |
| 이미지 | 고정 해상도, resize 규칙 직접 정의 | processor의 크기 정책 기록 |
| 정답 | 색·위치·순서 등 합성 label | 이 단계에서는 입력 표현 관찰 |
| 모델 | 내 축소 embedding | 관찰만으로도 가능; 원본 weight 불필요 |
| 비교 | shape/순서/위치 계약 | 실제 출력 grid와 placeholder 수 |

B의 token IDs를 A의 작은 embedding에 그대로 넣지 않습니다. special token만 바꿔 끼우는 것으로 전체 사전 변환이 되는 것도 아닙니다.

## 2. 합성 이미지 입력 표

64×64 RGB 한 장, patch16, temporal patch2, spatial merge2입니다. 정지 이미지는 temporal 2칸을 같은 이미지로 채우는 실습 규칙을 먼저 문서화합니다.

| 단계 | 값 |
|---|---|
| raw 이미지 | 64×64×3 |
| 실습 resize | 없음 |
| temporal 입력 | 동일 frame 2개 |
| grid_thw | [1,4,4] |
| vision patch 수 | 16 |
| merger 뒤 visual token | 4 |
| 필요한 image placeholder | 4 |

4개의 merged patch의 **지역 좌표**는 (t,h,w)=(0,0,0),(0,0,1),(0,1,0),(0,1,1)입니다. 실제 decoder position은 text 구간과의 offset을 포함합니다. 지역 좌표를 그대로 공식 global position이라고 부르지 않습니다.

## 3. Interleaved MRoPE를 작은 표로 확인

[축소 설정](../../../target/TINY_CONFIG.md)의 rotary8, sections=[2,1,1]이면 주파수 4칸을 t,h,w,t 축에 대응시키는 형태를 source와 대조합니다. 동일 token에서 h만 바꾸면 h에 배정한 주파수의 angle만 바뀌어야 합니다.

text-only input에서는 t/h/w에 같은 순차 위치를 주는 경로를 먼저 검사합니다. 이후 text→vision start→image placeholders→vision end→text의 위치 표를 손으로 적고 공식 get_rope_index와 비교합니다.

## 4. 공식 입력 관찰에서 기록할 것

원본 크기, resize 뒤 크기, 실제 frame sampling, grid_thw, input_ids 중 special ID의 위치, placeholder 수, timestamp 문자열, position IDs를 한 기록에 모읍니다. pixel tensor의 flattened 축은 raw RGB 배열과 같다고 가정하지 않습니다.

공식 이미지 설정의 size 값이 있는데 무조건 64×64가 유지된다고 계산하지 않습니다. [고정 processor 설정](https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/de4b8e4d43b917e7706784d8bb445c9af86a3540/preprocessor_config.json)과 README의 원문 링크를 참고합니다. 공식 경로의 position 생성 규칙을 합성 경로에 옮길 때 바꾼 규칙을 명시합니다.

### 공식 tokenizer를 작은 모델에 연결할 때

관찰 이후 실제 연결이 필요할 때만 다음 순서로 진행합니다.

1. tokenizer 전체 사전과 추가 special token의 ID를 저장합니다. 필요한 embedding 행 수는 사용하는 모든 ID의 최댓값+1 이상입니다.
2. 원래 ID를 유지하는 경로에서는 text config의 vocab 크기, input embedding 행 수, output head 행 수를 함께 맞춥니다. 작은 hidden을 쓰더라도 사전이 크면 메모리가 늘어납니다.
3. 합성 vocab으로 학습한 weight의 행 번호는 공식 단어 의미와 대응하지 않습니다. 새 embedding/head를 초기화하고, weight tying을 선택했다면 두 모듈이 실제로 공유하는지 확인합니다.
4. EOS/PAD/image/video/start/end ID도 config와 일치시킵니다. 모든 입력 ID가 범위 안인지 확인한 뒤 text-only → 이미지 하나 순서로 forward합니다.
5. 더 작게 유지하려면 제한된 fixture의 ID를 compact ID로 다시 매핑할 수 있지만, 이는 공식 tokenizer 전체 호환이 아닌 교육용 변환입니다. PLE hash도 ID에 의존하므로 hash 결과까지 공식과 같다고 비교하지 않습니다.

## 5. 단계별 통과

단일 이미지 → 크기가 다른 두 이미지 → text-only → padding batch → 짧은 영상 순서입니다. 각 단계에서 placeholder 수=merger token 수인지 먼저 확인합니다. 범위가 벗어나는 ID, 개수 불일치는 모델 계산 전에 잡습니다.


---

[주차 README로 돌아가 기록하기](README.md#checklist) · [기록 분할 규칙](../../WRITING.md)
