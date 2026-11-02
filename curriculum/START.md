# 시작 전 진단과 학습 약속

[홈](../README.md) · [1주차](01-basics/week-01/README.md) · [환경 준비](../target/ENVIRONMENT.md)

목표는 구조를 이해하고 **내 손으로 작은 모델을 구현하는 것**입니다. GPT에는 설명·힌트·검토를 요청합니다. 원본 규모의 학습이나 성능 복제는 완료 조건이 아닙니다.

## 6개 진단 — 답을 먼저 가리고 풀기

| 항목 | 문제 | 스스로 확인할 답/행동 |
|---|---|---|
| 행렬과 축 | Q=[2,3,4], K=[2,5,4]일 때 QKᵀ의 shape? | [2,3,5]. 마지막 두 축만 전치 |
| broadcasting | X=[2,3,4]에 bias=[4]를 더하면? | [2,3,4]. 같은 bias가 batch/token에 반복 |
| 미분·업데이트 | x=3,w=2,y=1,L=(wx−y)²의 dL/dw? | 30. 학습률0.01의 한 단계 뒤 w=1.7, L=16.81 |
| CE | 두 class logits가 [0,0]이고 정답0이면? | 확률 [0.5,0.5], loss=ln2≈0.693147 |
| 작은 학습 | 고정된 입력 4개를 작은 MLP로 학습할 수 있는가? | zero_grad→forward→loss→backward→step, 유한 gradient와 loss 감소를 기록 |
| 저장/복원 | 같은 입력·eval 상태에서 저장 전후를 비교할 수 있는가? | 가중치·config를 복원해 출력이 맞는지 비교; seed 재설정만으로 대신하지 않음 |

앞의 네 문제를 설명할 수 없으면 아래 해당 자료를 읽고 다시 풉니다. 뒤의 두 문제는 직접 작은 실습을 해 보되 코드가 낯설면 튜토리얼의 해당 항목부터 학습합니다. 이미 할 수 있으면 건너뜁니다.

## 부족한 부분만 보충하기

- **한국어·기초 실습:** [PyTorch 기본 익히기](https://tutorials.pytorch.kr/beginner/basics/intro.html)의 Tensor → Autograd → 최적화 → 저장/불러오기. [영어 공식 원문](https://docs.pytorch.org/tutorials/beginner/basics/intro.html)으로 API 의미를 대조합니다.
- **영어·학습 원리 영상:** [Karpathy Zero to Hero](https://karpathy.ai/zero-to-hero.html)의 micrograd는 미분, makemore는 문자 사전·next-token 학습에 대응합니다. 이미 이해하면 전편 시청하지 않습니다.
- **영어·GPT 구현 강의:** [Let's build GPT](https://www.youtube.com/watch?v=kCc8FmEb1nY)는 2주차의 decoder-only 연결 설명에 사용합니다. 강의 저자의 목차를 확인했으며 영상 전체 시청 검증은 하지 않았습니다.

프레임워크나 모델을 처음 다루는 시간이 16주 표 안에 자동으로 확보되어 있지는 않습니다. 진단에 필요한 시간을 먼저 쓰고, 16주는 순서로 활용합니다.

## 중간에 반드시 멈춰 확인할 지점

| 시점 | 다음 단계로 가기 전에 확인 | 아직 하지 않아도 되는 것 |
|---|---|---|
| 2주 뒤 | dense LM의 causal 검사·작은 배치 학습·생성·저장 | MoE·GDN·멀티모달 |
| 4주 뒤 | RoPE/GQA/cache와 expert 가중합 | 모든 기능을 8층에 동시 통합 |
| 8주 뒤 | GDN→GR→QSA→PLE를 각각 추가한 차이 기록 | fused kernel·대형 weight |
| 10주 뒤 | 단일 이미지 연결·정답 mask·gradient 경로 | 일반 이미지 대화 능력 |
| 12~14주 | 단일 이미지 성공 후 영상, 과제별 대조군 | 원본 benchmark 성능 |
| 15~16주 | 재실행 자료·실패 사례·재현 범위 | 미실행 검사를 통과로 표시 |

학습용 기본 완주는 작은 text LM과 이미지 과제를 설명·검증하는 단계입니다. 목표 구조의 축소 통합까지 가려면 이후 GDN/QSA/GR/PLE/영상 위치도 수행합니다. 기본 단계의 성공을 목표 구조 전체의 재현으로 부르지 않습니다.

## GPT에 도움을 요청할 때

> 이번 목표는 [단일 연산]입니다. 기준은 [논문 절/고정 코드 링크]입니다. 입력 shape·dtype는 [...], 내가 예상한 값은 [...], 실제 결과는 [...]입니다. 완성 코드를 쓰지 말고 틀린 축·가정·연산 순서부터 짚어 주세요. 힌트를 하나만 주고, 제가 고친 뒤 다시 검사해 주세요.

막히면 **용어 → shape → 손계산 → 한 모듈 → 전체 모델** 순서로 질문을 좁힙니다. GPT 답은 원문이나 독립된 작은 예제로 다시 확인합니다. 검색어에는 모델 이름보다 연산/증상을 넣습니다: “RoPE cache offset”, “causal depthwise convolution”, “QSA block tail”.

## 기록 위치

완료 체크는 주차 README 한 곳에서만 합니다. 워크북은 풀이 안내입니다. 긴 시행착오는 [문서 길이·기록 규칙](WRITING.md)에 따라 같은 주차 폴더의 NOTES-01.md, NOTES-02.md로 이어갑니다.

[준비됐으면 1주차로](01-basics/week-01/README.md)
