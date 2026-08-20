# how-dilated-conv-works

Dilated Convolution은 어떻게 풀링 없이 시야를 넓히는가 — 커널 원소 사이에 간격을 넣는 것만으로 수용 영역을 지수적으로 확장하는 원리를 브라우저에서 직접 실험하며 배우는 인터랙티브 교육 자료입니다.

**🔗 데모: https://dev-jonghoonpark.github.io/how-dilated-conv-works/**

> [how-ai-works](https://github.com/dev-jonghoonpark/how-ai-works) 시리즈의 일부입니다.

원 논문: [Multi-Scale Context Aggregation by Dilated Convolutions](https://arxiv.org/abs/1511.07122) (Yu & Koltun, ICLR 2016) · 함께 읽기: Sutskever's List 3장 「ResNet 혁명」 §3.5

## 다루는 내용

| 절 | 주제 | 인터랙티브 데모 |
|---|---|---|
| §1 | 풀링(pooling)이란 무엇인가 — 맥스/평균, 창·스트라이드, 왜 쓰는가 | 8×8 특징 맵에 풀링 적용, 출력 칸에 대응하는 입력 창과 살아남은 값 추적 |
| §2 | 문제 — 풀링은 밀집 예측(segmentation)의 정답을 지운다 | 64×64 분할 마스크를 맥스 풀링으로 줄였다 복원하며 IoU 붕괴 관찰 |
| §3 | 기존 해법의 딜레마 (큰 커널·풀링·층 쌓기) | 커널 크기 슬라이더로 파라미터 폭증 확인 |
| §4 | 발상 — 커널에 간격을 넣는다, `(F ∗ₗ k)(p) = Σ_{s+l·t=p} F(s)k(t)` | 확장률 1/2/4/8에 따라 3×3 커널이 읽는 위치 시각화 (파라미터는 항상 9개) |
| §5 | 수용 영역의 지수적 확장 `(2^{i+2}−1)²` | 논문 Figure 1 재현 — 기여 횟수 열지도 역추적, dilated vs 일반 3×3 스택 비교 |
| §6 | 실제 사진으로 확인하기 — 같은 필터를 확장률만 바꿔 적용 | 브라우저에서 실제 3×3 합성곱 수행: 커널이 읽는 9곳 오버레이, 풀링 경로(pool→conv→upsample) vs 확장 경로 비교, 내 사진 열기 지원 |
| §7 | 컨텍스트 모듈 (논문 Table 1) | 층별 수용 영역 하이라이트, Basic/Large 채널 전환 |
| §8 | 항등 초기화 — ResNet과 같은 "항등 근처에서 시작" 논리 | 무작위 vs 항등 초기화로 7층 신호 전파 비교 |
| §9 | 프런트엔드란 무엇인가(앞단 예측망 = 개조한 VGG-16) — 단순화와 PASCAL VOC 결과 | mIoU 비교 차트 (FCN-8s/DeepLab vs 단순화 프런트엔드, +컨텍스트 모듈) |
| §10 | 주의 — 격자 아티팩트(gridding effect) | 확장률 조합별 입력 커버리지 지도 (같은 확장률 반복 시 생기는 구멍) |
| §11 | 정리 — 세 번 반복된 하나의 교훈 | — |
| §12 | 이해도 퀴즈 | 5문항 |

## 실행

빌드 과정 없는 단일 `index.html`입니다. 브라우저로 열면 됩니다.

```bash
open index.html
```

모든 계산(맥스 풀링, 실제 이미지 합성곱, 수용 영역 역추적, 신호 전파 시뮬레이션)은 순수 JavaScript로 페이지 안에서 수행됩니다 — 외부 라이브러리·서버 없음.

§6의 샘플 사진은 [scikit-image](https://scikit-image.org/docs/stable/api/skimage.data.html) 데이터셋에서 가져와 256×256으로 줄여 임베드했습니다 — `astronaut`(NASA, 퍼블릭 도메인), `chelsea`(CC0, Stefan van der Walt), `brick`(CC0Textures).
