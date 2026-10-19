---
name: unslop
description: 모든 글에서 AI가 쓴 티가 나는 표현을 걷어냅니다.
disable-model-invocation: true
---

> 이 문서는 [SKILL.md](./SKILL.md)을 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Unslop

텍스트를 편집해 AI 패턴을 제거합니다. 아래 패턴을 찾아본 다음 다시 씁니다. 의미를 보존하고 의도한 어조에 맞춥니다.

## 찾아서 고칠 패턴

규칙 번호는 다른 스킬이 인용하는 고정 ID입니다. 규칙을 삭제하면 그 번호는 빈 채로 남습니다.

### 내용

3. **피상적인 -ing 구문.** "highlighting...", "ensuring...", "reflecting...", "showcasing...", "fostering..." 같은 구문입니다. 삭제하거나 실제 출처를 들어 내용을 보강합니다.
5. **모호한 출처 표기.** "Experts believe", "Industry reports suggest", "Some critics argue" 같은 표현입니다. 출처를 밝히거나 삭제합니다.

### 언어

7. **AI 어휘.** Additionally, crucial, delve, enduring, enhance, fostering, garner, interplay, intricate, landscape(추상적 의미), pivotal, showcase, tapestry(추상적 의미), testament, underscore, vibrant 같은 단어입니다. 평이한 단어로 바꿉니다.
8. **"is"를 화려하게 돌려 말하기.** "serves as", "stands as", "boasts", "features" 같은 표현입니다. "is"나 "has"라고 씁니다.
9. **"Not just X, but Y."** 대신 요점을 바로 말합니다.
10. **3의 법칙.** 생각을 억지로 세 개씩 묶지 않습니다. 자연스러운 개수를 씁니다.
11. **동의어 돌려쓰기.** 한 단락 안에서 protagonist, main character, central figure, hero를 모두 쓰는 경우입니다. 하나를 골라 반복해서 씁니다.
12. **거짓 범위.** X와 Y가 의미 있는 척도 위에 있지 않은데 "from X to Y"라고 쓰는 경우입니다. 주제를 직접 나열합니다.

### 스타일

13. **엠대시 남용.** 엠대시를 아예 쓰지 않습니다. 마침표나 쉼표만 씁니다(괄호, 엔대시, 대시 대용 하이픈도 쓰지 않습니다). 생각을 구분해야 한다면 문장을 끝내거나 쉼표를 씁니다.
14. **콜론 남용.** 콜론은 목록이나 예시 앞에서는 괜찮지만 문장 중간의 연결어로 쓰지 않습니다. "If you're coming from traditional automation: instead of registering event handlers, you describe conditions"에서 콜론은 아무것도 더하지 않습니다. 비교 틀 없이 요점만으로 성립하도록 다시 씁니다. "Describing when the scheduler should fire works best as plain English." 의미는 같고, 구두점에 기대지 않습니다.
15. **굵은 글씨 남용.** 모든 고유명사나 약어를 굵게 표시하지 않습니다.
16. **인라인 헤더 목록.** AI 티가 나는 형태는 굵은 레이블과 콜론이 그 줄의 내용을 되풀이하는 경우입니다. 예를 들어 "**Performance:** Performance improved..."와 같습니다. 이런 형태는 산문으로 바꿉니다. 마침표로 끝나고, 항목의 이름을 밝히며, 뒤에 실제로 새로운 내용이 이어지는 굵은 도입구("**Schema in TypeScript.** Tables live in one file.")는 괜찮으며 AI 티로 보지 않습니다.
17. **타이틀 케이스 제목.** 문장형 대소문자(sentence case)를 씁니다.
18. **장식용 이모지.** 제목과 글머리 기호에서 제거합니다.
19. **둥근 따옴표.** 곧은 따옴표를 씁니다.

### 대화의 흔적

20. **챗봇 문구.** "I hope this helps!", "Let me know if...", "Of course!", "Certainly!", "Found the smoking gun!" 같은 문구입니다. 제거합니다.
22. **아첨하는 어조.** "Great question! You're absolutely right!" 같은 표현입니다. 바로 본론으로 답합니다.

### 군더더기

23. **군더더기 구문.** "In order to"는 "To"로 바꿉니다. "Due to the fact that"은 "Because"로 바꿉니다. "It is important to note that"은 삭제합니다.
24. **과도한 얼버무림.** "could potentially possibly be argued that it might"는 "may"로 바꿉니다.
25. **뻔한 결론.** "The future looks bright." 같은 문장 대신 구체적인 계획이나 사실을 적습니다.

### 전문 용어

26. **추상적인 은유 명사.** Substrate(base), wedge, wedge in(add), vector(way, method), locus, vantage, nexus, 명사로 쓴 primitive, 은유로 쓴 harness, "API surface"에서와 같은 surface, bedrock, 은유로 쓴 scaffolding, modality, paradigm, gold-plating(작업에 필요한 것보다 더 많이 하는 것), 은유로 쓴 ratchet(메커니즘의 실제 이름, 또는 조여지기만 하는 한도로 바꿉니다), 코드를 옮기는 일을 뜻하는 evacuate(move out), endgame(마지막 단계), north star, flywheel 같은 단어입니다. 이런 단어는 기술적으로 들리지만 대개 더 평이하고 구체적인 단어가 있습니다. 구체적인 단어를 고릅니다.

### 평이한 말

27. **느낌이 아니라 하는 일을 말합니다.** "the database stays close at hand", "SQL you can read", "types that follow your schema"는 느낌을 말합니다. 고친 문장은 메커니즘이나 숫자를 말합니다. 예를 들어 "`.toSQL()` returns the exact string sent to the database", "a column rename fails the build"와 같습니다. 그 문장이 독자에게 무엇을 하거나 알라고 말하는지 묻고, 그 내용을 씁니다. 구체적인 지시, 사실, 숫자로 다시 말할 수 없다면 삭제합니다. 한 가지를 더 확인합니다. 그 문장이 다른 프로젝트의 문서에 그대로 들어가도 어색하지 않다면, 이 프로젝트에 대해서는 아무것도 말하지 않는 것입니다. 삭제합니다.
28. **빽빽한 문장은 줄이거나 나눕니다.** 독자가 문장을 해석하려고 되돌아가 읽어야 한다면 두 문장으로 나누거나 절을 덜어냅니다. 한 문장에는 하나의 생각만 담습니다.
29. **능동태.** 능동태를 선호합니다. "is/are/was/were + 과거분사"를 찾아내 행위자를 밝힙니다. "queries are validated"는 "the compiler validates queries"로, "the file is parsed by the loader"는 "the loader parses the file"로 바꿉니다. 수동태는 행위자를 모르거나 행위자가 정말 중요하지 않을 때만 괜찮습니다.
30. **부사를 빼거나 더 강한 동사를 씁니다.** "runs quickly"는 "is fast"나 실제 숫자로, "significantly improves"는 측정한 변화량으로 바꿉니다. 약한 동사를 부사가 떠받치고 있다면 동사를 잘못 고른 것입니다.
31. **평이한 단어를 선호합니다.** "utilize"와 "leverage"는 "use"로, "facilitate"는 "help"로, "numerous"는 "many"로, "in the event that"은 "if"로 바꿉니다. 더 화려한 동의어가 더 명확한 경우는 드뭅니다.
32. **꾸민 문체.** 문자 그대로의 표현이 있는데도 은유나 수식을 쓰는 경우입니다. 경구("wire it or delete it"), 효과를 노린 수사적 문장 조각, 의인화한 코드("the plan holds it"), 비유적 동사("rides along", "stands on"), 상투적인 틀 짓기 구문이 여기에 해당합니다. "A dial worth turning"은 "a parameter worth varying"으로 바꿉니다. 뜻하는 바를 그대로 말합니다. 은유 명사는 규칙 26에서 다룹니다.
33. **과도한 압축.** 관사를 빼거나, 동사 없는 문장 조각을 쓰거나, 기호식 표현과 약어를 써서 독자가 읽는 대신 해독하게 만드는 경우입니다. "Parser rejects bad date → exit 2, no write"는 "The parser rejects a bad date, exits with code 2, and writes nothing."으로 바꿉니다. 관사와 동사를 갖춘 완전한 문장을 쓰고, 화살표와 약어는 풀어 씁니다.
