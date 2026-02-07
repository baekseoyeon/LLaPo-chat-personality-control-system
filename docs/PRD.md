# PRD — LLaPo-chat Personality Control System
> Slider-based personality control (PV merging) + UX validation (A/B)

---

## 0. Context
LLM 성격을 **프롬프트가 아닌 모델 레벨(Personality Vector merging)** 로 미세 조절하고,  
이를 사용자가 직접 조절 가능한 **슬라이더 UX**로 제공했을 때 UX(특히 Enjoyment / User-control)가 실제로 개선되는지 검증한다.

* 슬라이더 UX: 카카오 같이가치 big5 성격검사 참고
<p align="center">
  <img src="../image/카카오-ocean.png" width="300" alt="Personality Vector Merging Architecture" />
</p>

- 연구 스타일: **HCI/UX 중심 + 모델 제어(merging) 기반 구현**
- 평가 구조: **사용자에게 task 수행 → 설문 + LLM eval(의도대로 움직였는지)**

---

## 1. Goal
사용자가 대화형 AI의 성격(방향/강도/조합)을 **직접 조절**할 수 있게 하고,  
이 조절 경험이 **통제감(User-control)** 및 **즐거움(Enjoyment)** 에 주는 영향을 측정한다.

### Success Criteria (v1)
<p align="center">
  <img src="../image/llapo-chat-초안.png" width="600" alt="Personality Vector Merging Architecture" />
</p>

- 사용자는 슬라이더만으로 “성격이 바뀌었다”를 체감한다.  
- 실험 설계에서 A/B 조건이 **동일한 흐름**으로 진행된다(차이는 control 가능 여부만).
- 로그/시간/phase가 안정적으로 기록된다(실험 운영 가능 수준).

---

## 2. Non-goal
- “정답률/정보 정확성” 자체를 올리는 모델 개선
- 특정 도메인(의료/법률 등) 프로덕션 배포
- 멀티모달/멀티에이전트 확장(향후 v2)

---

## 3. Target User (participant persona)
- 일상 대화형 챗봇을 “내 스타일”로 쓰고 싶은 사용자
- 캐릭터/톤을 바꾸며 대화하는 것을 즐기는 사용자
- 상담/동반자형 사용에서 “대화 몰입”을 중요시하는 사용자

---

## 4. Key Questions (what we want to learn)
- 사용자는 성격을 “조절했다”는 감각을 실제로 느끼는가?
- 어떤 trait는 조절이 잘 체감되고, 어떤 trait는 안 되는가?
- multi-trait 조합 시 성격 강도가 약해지는 문제를 UX로 어떻게 보완할 수 있는가?
- control UX는 “만족”보다 “재미/즐거움”에 더 큰 영향을 주는가?

---

## 5. Core Use Cases (v1)
1) 사용자가 원하는 성격 조합으로 대화 시작  
2) 대화 중 “이거 아닌데?” → Reset → 재조절 → 다시 대화  
3) 라운드별로 (조절값 / 대화 로그 / 설문)을 기록해 A/B 비교 분석

---

## 6. System Scope (Architecture)
**User ↔ Streamlit(App) ↔ FastAPI(API Server) ↔ LLM**

### 6.1 Model control (PV merging)
- Base 모델 + 성격 모델(10개)로부터 **delta vector(Δ)** 생성  
- API에서 delta에 scaling coefficient를 곱해 Base에 더해 **merged model** 생성
- `/merge` : 현재 slider 설정을 모델에 반영  
- `/chat` : 대화 응답 반환

> v1 구현에서 `.pt`는 메모리/로딩 문제가 커서 `.safetensors` 기반(지연 로딩/zero-copy)로 전환.

---

## 7. Product Concept
### 7.1 A/B Session Definition
- **LLaPo-chat**: 사용자가 슬라이더로 personality control 가능
- **LLaPo-base**: 슬라이더 OFF(= personality=0), 동일 시나리오 수행

> 실험 순서 효과를 줄이기 위해 A/B 시작 순서는 participant마다 counterbalanced 가능.

### 7.2 Personality control rule (budgeted control)
**문제:** trait를 많이 섞을수록 각 trait 강도가 약해져 성격이 안 느껴짐 
**결정(v1):** “L1 정규화” 대신 사용자가 이해 가능한 **성격 예산(budget)** 으로 해결

- UI에서 사용자는 총 **Personality Budget(예: 5 또는 10 포인트)** 내에서 trait를 분배
- 내부 API 계수는 `point * 0.2`로 변환(최대 총합이 안전 범위(≤2.0) 내로 유지되도록 설계)
- direction: 0 기준 왼쪽 = Low, 오른쪽 = High
- 목표: “1개만 조절해도 너무 과하고, 5개 조절하면 너무 약한” 문제를 UX에서 완화

---

## 8. UX Flow (phased flow)
### Phase overview
1) **Notice**: 실험 안내 / 주의사항  
2) **Adjust**: 슬라이더 조절 + Apply  
3) **Merging**: 모델 병합 진행(대기)  
4) **Chatting**: 시나리오 기반 대화(타이머)  
5) **Reset-survey**: 리셋 후 설문(인지된 성격/경험 기록)  
6) **Base Round**: 슬라이더 OFF로 동일 시나리오 반복  
7) **Finish**

### UI Interaction Rules (실험 안정성용)
- **merging 중**: 채팅/슬라이더/버튼 모두 비활성화
- **chatting 중**: Reset만 활성화(나머지 비활성화)
- **reset_survey 중**: 사이드바 전체 비활성화

> Streamlit rerun/autorefresh 충돌로 메시지 누락이 발생할 수 있어, v1에서는 rerun 중복 방지 및 refresh 조건을 강화.

---

## 9. Requirements
### 9.1 Functional (Must)
- Big Five trait slider (single/multi-trait)
- Apply → merge stage → chat
- 시나리오 진행 (Scenario 1 → 2 → 3) + 각 라운드별 로그 저장
- Reset 버튼: reset_survey 호출 + 설정 초기화 + phase/시간 기록
- Base 라운드: slider OFF로 동일 시나리오 수행

### 9.2 Non-functional (Should)
- 실험 환경 통제를 위해 동일 PC/동일 UI 흐름 유지
- waiting time/latency 안내 문구 제공(“약 3분 내외” 등)
- 병합 및 생성 중 UI 입력 lock 확실히 적용
- 로그는 분석 가능한 구조로 저장(phase, scenario_id, personality_coeff, messages, timestamps)

---

## 10. Metrics (Success Criteria)
### 10.1 UX Metrics (Primary)
- **Satisfaction** (7 items)
- **Enjoyment** (9 items)
- **User-control** (조절 경험 및 통제감 관련)

> Likert scale: 1–7 (전혀 아니다 ~ 매우 그렇다)  
> 한국어/영어 버전 모두 준비 가능(실험 환경에 따라 선택)

### 10.2 LLM Evaluation (Secondary, optional in v1)
- 사용자가 설정한 대표 성격(main/sub trait)이 실제 응답에 반영되었는가?
- “인지된 성격(perceived)”과 “설정한 성격(configured)”의 정합성

---

## 11. Known Risks & Mitigations (from implementation)
### R1) Multi-trait에서 성격이 약해짐
- **Mitigation:** budget-based control (유저가 직접 분배)

### R2) 모델 병합에서 메모리/로딩 병목
- **Mitigation:** `.safetensors` + lazy loading / sequential load
- DARE 적용 시 큰 weight(lm_head/embed)는 예외 처리(안정성 우선)

### R3) Chat template / tokenizer mismatch로 CUDA assert
- **Mitigation:** pad_token을 eos_token으로 강제 설정, apply_chat_template 기반으로 정렬
- lm_head 크기와 tokenizer length mismatch 디버깅 포함

### R4) assistant 역할 붕괴(너무 공격적/이상 출력/이름 hallucination)
- **Mitigation:** system prompt에 assistant role + behavioral rules 명시
- 필요 시 “functional trait definition”을 prompt에 함께 제공(사용자에게 보여주는 도움말과 일관)

### R5) Streamlit rerun + autorefresh로 message drop
- **Mitigation:** rerun 중복 방지 로직 + refresh 조건 강화

---

## 12. Open Questions (v2 candidates)
- personality budget(5 vs 10)의 최적점은?
- trait label을 Big Five 원어 그대로 둘지, **사용자 친화적인 기능형 label**로 바꿀지?
- scenario 기반 대화에서 personality 유지/감쇠를 어떻게 측정할지?
- latency가 UX에 미치는 영향(merge 대기시간 vs 체감 가치)
