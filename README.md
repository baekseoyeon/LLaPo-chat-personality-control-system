# LLaPo-chat: 슬라이더를 통한 AI 성격 부여 기반의 사용자 중심 Chatbot 시스템
### LLaPo-chat: Slider-based Personality Control for LLM Chatbots
<p align="center">
  <img src="image/LLaPo-chat.png" width="800" alt="LLaPo-chat Overview" />
</p>

### Paper
One Model Fits You: Personality Customization in LLM-based Chatbots via Model Merging (CHI 2026 투고중)

---

### LLaPo-chat Project
> 사용자가 슬라이더로 LLM의 Big Five 성격(방향/강도/조합)을 조절하고 그 “통제감(User-control)”이 대화 UX(특히 Enjoyment)에 미치는 영향을 검증한 대화형 AI 개인화 제어 시스템입니다.

---
## 1. 문제 정의
Problem
대화형 AI는 답변 품질이 좋아도 사용자가 원하는 말투/성격/대화 스타일을 일관되게 통제하기 어렵습니다.  
특히 “내가 조절했다”는 감각(통제감)이 약하면 재미/몰입 같은 경험 가치가 떨어질 수 있습니다.

---
## 2. 해결 전략
Approach
### 1) 모델 수준 성격 제어: Personality Vector + Scaling + Multi-trait Merge
- 각 성격 조건 p에 대해 fine-tuning으로 얻은 personality vector ϕp를 정의하고 사용자가 고른 강도를 scaling coefficient로 반영합니다. 
- 최종 적용은 다음 형태의 합성으로 구현됩니다:  
  **θ′ = θbase + Σ cp ϕp** (multi-trait composition)
* Repository:[ Scalable-Personality-Control-Architecture-for-LLM-based-AI-Agents](https://github.com/baekseoyeon/Scalable-Personality-Control-Architecture-for-LLM-based-AI-Agents)를 참고해주세요!
* paper: [![arXiv](https://img.shields.io/badge/arXiv-2509.19727-b31b1b.svg)](https://arxiv.org/abs/2509.19727)

### 2) 사용자 통제 UX: Slider → Merge → Chat → Reset/Survey
<p align="center">
  <img src="image/llapo_chat_flow.png" width="800" alt="LLaPo-chat Overview" />
</p>

- LLaPo-chat은 (a) 성격 조절 (b) 머지 (c) 대화 단계로 구성됩니다.
- 사용자는 언제든지 부여한 성격을 변경 및 리셋 가능합니다.
- 리셋 시점에 “내가 실제로 느낀 성격”을 기록해 조절-인지 정합성(사용자가 부여한 성격 vs 사용자가 실제로 느낀 성격) 분석이 가능하도록 설계했습니다.

### 3) A/B 비교 구조: LLaPo-chat vs LLaPo-base
- **LLaPo-chat**: 사용자 선택에 따라 1개 이상 personality vector를 머지해 성격 조절 가능
- **LLaPo-base**: 동일 백본이지만 성격 조절 기능 없음

---

## Validation
- 참가자 **30명**, **within-subject**로 두 조건(LLaPo-chat/base) 모두 경험 
- 세션 구성: **3개 토픽 × 각 5분 대화**, 토픽 라운드별 로그 저장
- 설문: Satisfaction(1–6), Enjoyment(7–15), User-control(16–33) 측정

---

## Results
- 15개 공통 문항 평균에서 **LLaPo-chat이 LLaPo-base보다 높음** (5.044 vs 4.360, p=0.015)
- 세부적으로는 **Enjoyment가 유의미하게 증가** (p=0.002), Satisfaction은 유의미 차이 없음(p=0.174)

---

## Product takeaways
- “성격을 조절할 수 있다”는 기능 자체가 경험 가치(Enjoyment)를 올릴 수 있음
- 동일 백본/유사 구현에서 사용자 통제 UX가 체감 가치를 바꿀 수 있음

---

## 리포 구조
Structure

- `image/` : 아키텍처 이미지  
- `docs/` : 기획 문서
 - `docs/PRDv3_최종.md` ： 최종 PRD 문서

---
