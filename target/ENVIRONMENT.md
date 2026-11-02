# 실습 환경 준비 — 기본 실습과 reference 대조

[시작 전 진단](../curriculum/START.md) · [축소 설정](TINY_CONFIG.md) · [다음: 수치 대조](PARITY.md)

이 페이지는 사용자가 환경을 준비하는 절차입니다. 설치·실행을 대신 수행한 기록이 아닙니다. 처음에는 기본 실습만 준비하고, HF reference는 필요한 주차에 별도 환경에서 다룹니다.

## 1. 기본 실습 환경

1. 프로젝트별 가상환경을 하나 만듭니다. Python 3.11을 시작 후보로 삼되 설치하려는 PyTorch wheel의 지원 범위를 확인합니다.
2. [PyTorch 공식 설치 선택기](https://pytorch.org/get-started/locally/)에서 운영체제·패키지 관리자·CPU를 고른 안내를 따릅니다. 이번 기준 실험은 CPU·FP32입니다.
3. [기본 튜토리얼](https://docs.pytorch.org/tutorials/beginner/basics/intro.html)에서 tensor 연산, 작은 gradient, 저장/복원이 되는지 확인합니다.
4. Python/PyTorch 버전·운영체제·CPU·RAM을 기록합니다. 확인된 환경은 학습 도중 임의 업그레이드하지 않습니다.
5. 1~2주에는 Transformers, FlashAttention, Triton, GPU kernel 설치가 필요하지 않습니다. 기본 dense LM의 의미부터 확인합니다.

문제가 있으면 설치 로그와 import 단계부터 구분합니다. 모델 수식이 아직 실행되지 않은 설치 오류를 attention 구현 오류로 취급하지 않습니다.

## 2. 공식 reference 전용 환경

| 항목 | 기준 |
|---|---|
| Transformers | commit 98110a1f5332a9ac5629ccc2aeb6412393df15e9 |
| Python | 해당 commit setup.py의 지원 표는 3.11~3.14, python_requires는 >=3.11 |
| PyTorch | 해당 commit 의존성은 torch>=2.6. 하한만 맞췄다고 모든 조합이 검증되는 것은 아님 |
| 입력·가중치 | 작은 config를 직접 만들고 랜덤 또는 고정 가중치 사용 |
| checkpoint | 거대한 원본 weight를 내려받지 않음 |
| backend | 먼저 eager/기본 tensor 경로. optional GPU kernel은 별도 단계 |

[고정 setup.py](https://github.com/huggingface/transformers/blob/98110a1f5332a9ac5629ccc2aeb6412393df15e9/setup.py)의 패키지 조건과 [공식 설치 문서](https://huggingface.co/docs/transformers/installation)의 source 설치 절차를 대조합니다. 최신 main을 설치해 놓고 고정 commit과 비교했다고 기록하면 안 됩니다.

모델 파일 하나를 복사해서 실행하면 내부 helper·cache·decorator 의존성이 빠질 수 있습니다. 고정 repository 전체 패키지 환경을 준비하고 [코드 지도](CODE_MAP.md)의 모듈부터 확인합니다. 생성한 모델의 parameter 수가 예상 작은 값인지 확인한 뒤 forward를 실행합니다.

## 3. 단계별 통과 기록

| 단계 | 사용자가 남길 결과 | 실패하면 먼저 볼 것 |
|---|---|---|
| 패키지 import | 버전·경로 | 다른 가상환경 실행 여부 |
| 기본 tensor/gradient | shape·수치 | dtype·장치·autograd 사용 |
| 작은 module 생성 | config·parameter 수 | 원본 기본값이 남았는지 |
| 한 token forward | 입력·출력 shape | head/rotary/branch 차원 |
| 짧은 sequence | 유한 출력·mask 결과 | 빈 유효 행·position |
| cached decode | full 재계산과 오차 | cache offset·state reset |

CPU에서 특정 reference 경로가 지원되지 않으면 exception과 해당 경로를 기록합니다. 대체 연산의 손계산 검사는 계속할 수 있지만, 실행하지 못한 공식 대조는 미검증으로 남깁니다. 아직 확인하지 않은 호환 조합이나 처리 시간을 문서의 보장값으로 해석하지 않습니다.

## 환경 기록 양식

- 날짜 / 운영체제 / Python:
- PyTorch / Transformers version 및 commit:
- CPU·GPU / RAM·VRAM / dtype / backend:
- 성공한 최소 연산:
- 실패 로그와 재현 입력:
- 현재 사용할 수 있는 비교 수준:

이 표는 자신의 주차 기록에 한 번 적고 이후에는 링크로 참조합니다. 환경이 바뀌면 새 기록을 만들고 이전 결과와 구분합니다.
