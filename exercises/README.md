# 작은 실습 모음

[저장소 홈](../README.md) · [실험 기록 양식](../templates/experiment.md)

주차별 과제는 손계산·그림·표만으로도 완료할 수 있습니다. 구현을 추가한다면 `exercises/week-XX/` 폴더를 만들고 코드와 실험 기록을 함께 저장합니다. 아직 실행된 코드나 벤치마크 결과는 없습니다.

## 추천 확인 과제

| 주차 | 확인할 것 | 검증 방법 |
|---|---|---|
| 1 | Attention shape·causal mask | 미래 위치가 mask되는지 작은 행렬로 확인 |
| 2 | RoPE 상대 위치 | 위치 차이가 같은 Q/K 쌍의 회전 내적 비교 |
| 3 | KV cache | 2 × batch × layers × tokens × KV heads × head dim × bytes로 계산 |
| 5 | Linear Attention | 같은 kernel·정규화에서 batch 계산과 recurrent update 비교 |
| 6 | Delta rule | 같은 key에 다른 value를 넣어 상태 갱신 비교 |
| 8 | Patch token | 해상도/patch 크기별 patch 개수와 CLS 포함 여부 확인 |
| 9 | Contrastive 학습 | 작은 similarity 행렬과 정답 pair 표시 |
| 10~11 | VLM 연결 | vision feature→projector/merger→LLM의 차원 추적 |

## 기록 원칙

- 계산식의 가정, 라이브러리 버전, 입력 크기, dtype을 적습니다.
- FlashAttention 비교는 부동소수점 오차를 고려합니다. CPU의 작은 예제로 GPU 가속 성능을 일반화하지 않습니다.
- 큰 모델 다운로드·학습·유료 API는 필수 과제가 아닙니다.
- 코드가 없으면 해당 주차 README나 논문 NOTES에 풀이를 남깁니다.
- 모델 가중치·데이터셋·캐시·비밀키는 커밋하지 않습니다.
