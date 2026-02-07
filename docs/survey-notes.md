## 설문 문항 구성

## 1. 설문 문항

### Satisfaction (1–6)
1. I feel satisfied while using LLaPo-chat.  
2. I feel as if I am having an ongoing conversation through this interaction.  
3. Overall, I am satisfied with my experience of using LLaPo-chat.  
4. Using LLaPo-chat makes me feel good.  
5. My experience with LLaPo-chat meets my needs well.  
6. My experience with LLaPo-chat is close to my expectations.  

### Enjoyment (7–15)
7. I enjoy interacting with LLaPo-chat.  
8. I find it interesting to interact with LLaPo-chat.  
9. I found my interaction with LLaPo-chat enjoyable.  
10. I feel entertained when I use LLaPo-chat.  
11. I enjoy having conversations with LLaPo-chat.  
12. I find conversations with LLaPo-chat enjoyable and pleasant.  
13. I find conversations with LLaPo-chat fun.  
14. Using LLaPo-chat feels interesting to me.  
15. I feel a sense of entertainment when using LLaPo-chat.  

### Perceived Control (16–33)
16. I feel that LLaPo-chat reflects my intentions.  
17. I feel that LLaPo-chat meets my expectations.  
18. I feel that LLaPo-chat operates as I intend.  
19. I feel that LLaPo-chat behaves in a predictable manner.  
20. I became quickly familiar with adjusting LLaPo-chat’s personality.  
21. I feel that the personality adjustment feature of LLaPo-chat is appropriate.  
22. I am confident that I was able to adjust LLaPo-chat’s personality.  
23. I feel that I control LLaPo-chat’s personality during the interaction.  
24. I feel that the LLaPo-chat with the personality I adjusted is a good conversational partner.  
25. I feel that LLaPo-chat supports me rather than interferes with me.  
26. I feel that I am not in control of LLaPo-chat’s personality. (reverse)  
27. I feel that LLaPo-chat’s personality suits me well.  
28. I feel that the personality of LLaPo-chat differed from my expectations. (reverse)  
29. The personality customization feature provides sufficient information.  
30. The personality customization feature has no information missing.  
31. Conversations with LLaPo-chat feel like talking to a real person.  
32. LLaPo-chat exhibits a consistent, recognizable personality.  
33. I can use LLaPo-chat for everyday tasks.  

Notes  
- 역문항: 26, 28  
- 설문 제시 방식(v1): 참가자에게는 만족/즐거움/통제감 같은 분류를 보여주지 않고, 번호만 연속으로 제시해 응답 편향을 줄인다.
- Satisfaction & Enjoyment: 공통문항s
- Perceived Control: LLaPo-chat 단독문항s

---
### 8/28: CloChat 참고 및 베이스라인 고민
맥락  
- CloChat은 GPT-4 기반으로, 프롬프트를 통해 페르소나를 커스터마이즈하는 방식으로 동작한다.  
- CloChat에서는 참가자 32명, 1인당 총 12개의 대화 세션을 수행했다.  
- CloChat 6세션 + ChatGPT 6세션 구조로 비교했다.

핵심 고민  
- 페르소나/성격 커스터마이즈 연구에서 “무엇을 baseline으로 둘 것인가?”

후보안  
1) 기본 모델(바닐라) 기반  
- 성격 조절 UI 없음, 기본 어시스턴트 동작

2) 프롬프트 기반  
- 모델은 동일하되 프롬프트로만 페르소나를 조절

3) 최종결정
- LLaPo-base: 성격 조절 UI 없음, 모델 병합 없음  
- LLaPo-chat: 사용자가 슬라이더로 성격을 조절하고, 병합을 통해 반영됨  
- 추후 확장(선택): “프롬프트 기반 조절”을 제3조건으로 추가해 분리 효과를 재검증

---

## 3. 참가자 조건 관련 메모

### 9/30: 참가자 프로필 가정(초안)
- 영어 구사가 자유로운 사용자  
- LLM 사용 경험이 있는 사용자(예: ChatGPT)

사전 설문/인터뷰 아이디어(선택)  
- 사용 경험(빈도/사용 맥락)  
- 현재 LLM의 강점/약점 인식  
- 페르소나 커스터마이즈에 대한 니즈/선호

---

## 4. 통제감(Perceived Control) 문항 구성 의도

v1 통제감 문항이 커버하는 것  
- 사용자의 의도 반영/기대 충족  
- 예측 가능성, 일관된 성격 인지  
- 조절 기능의 학습 용이성, 조절 자신감  
- 상호작용에서의 지원성(방해 vs 지원)  
- “사람과 대화하는 느낌”과 같은 자연스러움

운영 메모  
- 참가자가 직접 성격을 설정한 조건에서는 주관적 편향 가능성이 있어, 대화 로그 기반의 별도 평가(annotator: 사람/LLM) 도입 가능성을 열어둔다.

---

## 5. 실험 전 설명

### 10/10: 성격 조절 기능 안내
- 이 실험에서 참가자는 대화형 AI의 “대화 스타일”을 직접 조절할 수 있다.  
- 조절은 한 가지 성향만 강하게 주는 방식도 가능하고, 여러 성향을 적당히 섞는 방식도 가능하다.  
- 조절 결과는 대화 중 말투, 반응 방식, 대화 전개 스타일에 영향을 준다.  
- 중요한 점은 “정답을 맞히는 능력”을 바꾸는 것이 아니라, “대화하는 방식”을 바꾸는 실험이라는 것이다.  
- 참가자는 대화 중 언제든지 초기화(Reset) 후 다시 조절할 수 있으며, 이는 “내가 원하는 스타일로 바뀌었는지”를 확인하기 위한 과정이다.

---

## 6. 포인트 시스템 및 설문 UX 운영 메모

### 10/11: 포인트 시스템(운영)
- UI에서 사용자는 제한된 포인트 안에서 성격을 분배해 조절한다.  
- 내부적으로는 포인트가 계수로 변환되어 병합에 사용된다.

설문 운영 단순화(v1)  
- LLaPo-base 이후 설문에서는 참가자 번호/나이/성별 같은 정보는 재수집하지 않는다.  
- 최종 설문은 문항 분류 라벨 없이 번호만 연속 제시한다.

---

## 7. 다음으로 남은 질문
- 프롬프트 기반 조절을 별도 조건으로 추가할지?  
- 참가자 자기평가 외에, 로그 기반 외부 평가(annotator: 사람/LLM)를 도입할지?  
- 외부 평가를 한다면 “성격 구현”과 “과업 도움”을 분리해서 평가할 루브릭을 어떻게 설계할지?
