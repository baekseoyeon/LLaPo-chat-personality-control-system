# PRD v3 Final — LLaPo-chat Personality Control System
Slider-based personality control (Personality Vector merging) + UX validation (A/B within-subject)

## 0. Context
- 목표: LLM 성격을 프롬프트가 아니라 모델 레벨(Personality Vector merging)에서 미세 조절하고,
  사용자가 슬라이더로 직접 조절 가능할 때 UX(특히 Enjoyment, User-control)가 개선되는지 검증한다.
- 연구 스타일: HCI/UX 중심 + 모델 제어(merging) 기반 구현
- 평가 구조: 사용자 task 수행 → 설문 + (옵션) LLM eval/로그 기반 분석

## 1. Goal
- 사용자가 대화형 AI의 성격(방향/강도/조합)을 직접 조절
- 조절 경험이 통제감(User-control) 및 즐거움(Enjoyment)에 미치는 영향 측정

Success criteria (운영 가능 기준)
- 슬라이더 조절만으로 성격 변화 체감
- A/B 조건이 동일 흐름으로 진행(차이는 control 가능 여부만)
- phase/시간/로그가 안정적으로 기록되고 실험 운영 가능

## 2. Non-goal
- 정답률/정보 정확성 자체를 올리는 모델 개선
- 특정 도메인(의료/법률 등) 프로덕션 배포
- 멀티모달/멀티에이전트 확장(v2 이후)

## 3. Target Users
- 일상 대화형 챗봇을 내 스타일로 쓰고 싶은 사용자
- 캐릭터/톤 변화에 흥미를 느끼는 사용자
- 상담/동반자형 맥락에서 몰입을 중시하는 사용자

## 4. Key Questions (Research)
- 사용자는 성격을 조절했다는 감각을 느끼는가?
- 어떤 trait는 체감이 잘 되고, 어떤 trait는 덜 되는가?
- multi-trait 조합에서 강도 약화 문제를 UX로 완화할 수 있는가?
- control UX는 만족보다 즐거움에 더 큰 영향을 주는가?

## 5. System Scope (Architecture)
- User ↔ Streamlit(App) ↔ FastAPI(API Server) ↔ LLM
- Endpoints
  - /merge: slider 설정을 모델 병합에 반영
  - /chat: 대화 응답 생성

Model control (Personality Vector merging)
- base 모델 + 성격 모델(10개)에서 delta vector(Δ = θ_trait - θ_base) 생성
- API에서 Δ * coefficient를 base에 더해 merged model 구성
- 저장 포맷: safetensors 기반(지연 로딩/zero-copy) 우선

Merging 안정화(운영 정책)
- 다중 벡터 병합 시 interference 완화를 위해 DaRE(드롭+리스케일) 사용
- 매우 큰 weight(lm_head, embed_tokens 등)는 안정성 이유로 DaRE dropout 제외(스케일-only 또는 CPU 처리)
- GPU threshold 기반으로 파라미터를 순차적으로 올려 처리하여 OOM 위험 완화

## 6. Personality Control Rule
문제
- 여러 trait를 섞을수록 각 trait 강도가 약해져 성격이 안 느껴지는 문제 발생

최종 해결: Personality Budget (UX-friendly)
- 사용자는 총 Personality Budget 안에서 trait 포인트를 분배한다.
- 포인트 합계는 UI에서 제한하여 안전한 총합이 자동 보장되도록 한다.

최종 파라미터 규칙
- 총 예산: 10 포인트. 최종 운영에서는 아래를 기준으로 한다.
  - 총합 제한: 1 ≤ Σ n_p ≤ 10
- 내부 계수 변환: c_p = 0.2 * n_p
- 안전 범위 자동 보장: Σ c_p ≤ 2.0
- 방향성: 각 trait는 한 축에서 High 또는 Low 중 한 방향만 선택해 조절
- 병합 식: θ' = θ_target + Σ (c_p * ϕ_p)

참고(UX 설명)
- 최종 버전에서는 사용 가능한 성격 예산을 10으로 고정
- 사용자는 Big5 슬라이더 합이 10을 넘을 수 없으며, 초과 시 추가 조정이 제한
- 예산을 나눠 쓰면 여러 성격을 섞을 수 있지만 각 성격 강도는 약해질 수 있음
- 강한 변화를 원하면 한두 개 trait에 포인트를 집중
- 각 trait 강도는 예산 내에서만 배분 가능

## 7. UX Flow
Phase overview
1) Notice: 실험 안내/주의사항
2) Adjust: 슬라이더 조절 + Apply
3) Merging: 모델 병합 진행(대기)
4) Chatting: 토픽 기반 자유 대화(타이머)
5) Reset-survey: Reset 후 perceived personality 설문 + 재조절 가능
6) Base session: 슬라이더 OFF로 동일 토픽 반복
7) Finish

UI interaction lock rules
- merging 중: 채팅/슬라이더/버튼 모두 비활성
- chatting 중: Reset만 활성, 나머지 비활성
- reset_survey 중: 사이드바 전체 비활성
- base phase: 슬라이더 없음

토픽 구성(최종 3개)
- What happened over the weekend
- Recent concerns or worries
- Current interests or hobbies
운영
- 토픽 당 5분 대화, 3개 토픽 순차 진행
- 토픽 전환 시 이전 대화는 archive하고 내부 메모리도 토픽 단위 정리

Reset 기능
- 언제든지 Reset 가능(채팅 중)
- Reset 시 직전 대화에서 느낀 perceived 성격을 보고하는 reset-survey 표시
- 제출 후 성격값 초기화 → adjust 단계로 복귀

## 8. Prompt Policy
- 모델 병합(PV)은 성격 강도를 파라미터 레벨에서 반영
- 프롬프트는 assistant 역할/안전/과업 수행 규칙을 정의하는 용도로 제한
- system prompt 구성
  - trait_desc_text: 선택된 trait 라벨을 자연어로 요약(사용자 UI 라벨과 일관)
  - Behavioral Rules:
    - helpful assistant, 충분히 상세히 답변
    - 사용자/assistant 이름 발명 금지
    - 1:1 대화체 유지
    - 과업에 따라 구조를 조절(개념/실무/창작)

## 9. Experiment Design
세션 정의(A/B)
- LLaPo-chat: 슬라이더 ON, personality control 가능
- LLaPo-base: 슬라이더 OFF(성격=0), 동일 UI 흐름과 토픽 진행
- within-subject로 두 조건 모두 경험
- 순서 효과 완화: counterbalanced(A→B, B→A)로 그룹 배정

측정
- Post-survey
  - [survey-notes.md](https://github.com/baekseoyeon/LLaPo-chat-personality-control-system/blob/main/docs/survey-notes.md)에 기재 
  - 공통(1–15): Satisfaction(6) + Enjoyment(9)
  - LLaPo-chat만 추가(16–33): User-control(18)
  - reverse-coded: 26, 28
- 추가 분석(옵션)
  - 조작값(prev) vs 지각값(perceived) 상관(조작 점검)
  - 로그 기반 LIWC 분석(you/we/i, Social, Conversation, nonflu 등)

분석 계획
- LLaPo-chat vs LLaPo-base: 대응표본 t-test(공통 15문항 및 하위척도)
- LLaPo-chat 내: User-control → Satisfaction/Enjoyment 선형회귀
- 보조: 조작-지각 정렬 상관, 대화 로그 언어지표 비교

## 10. Non-functional Requirements
- 동일 PC/동일 UI 흐름으로 실험 통제
- latency 안내 문구 제공(merge는 수분 단위)
- rerun/refresh 충돌 완화(중복 방지 로직, autorefresh 조건 강화)
- 로그 저장 구조
  - phase, scenario_id, personality_coeffs, messages, timestamps
  - reset 이벤트 및 설문 응답 포함

## 11. Known Risks and Mitigations
- Multi-trait에서 강도 약화
  - budget-based control로 UX에서 관리(집중 vs 분산 선택)
- 모델 병합 메모리/로딩 병목
  - safetensors + lazy loading, 순차 처리
- tokenizer/template mismatch(CUDA assert)
  - pad_token=eos_token 설정, apply_chat_template 사용, vocab/lm_head 크기 점검
- assistant 역할 붕괴/공격적 출력/이름 hallucination
  - system prompt에 역할 규칙 명시, 이름 발명 금지
  - 필요 시 extreme 조합에 대해 UI 경고 문구 제공
- Streamlit rerun + autorefresh로 message drop
  - rerun 중복 방지, refresh 조건 강화, 실험 안내에서 “간헐적 누락 가능” 고지

## 12. Final Deliverables
- Streamlit 앱(phase-based UI lock 포함)
- FastAPI 서버(/merge, /chat)
- personality delta vectors(.safetensors)
- 로그 저장 및 분석 스크립트(설문, 상관/회귀, LIWC)

---
