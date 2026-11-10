---
name: gaeddeok-chalddeok
description: Turn messy, rambling, unstructured user ideas into a clean, well-structured prompt. Use this skill whenever the user dumps a disorganized stream-of-consciousness idea, brain-dump, or vague half-formed request and wants it turned into a usable prompt — phrases like "정리해줘", "프롬프트로 만들어줘", "이거 프롬프트로", "개떡같이 말할테니 찰떡같이", "turn this into a prompt", "clean this up into a prompt", or any time the input reads as a jumbled idea-dump rather than a finished instruction. Trigger even when the user doesn't say the word "prompt" but is clearly off-loading a messy idea expecting structure back.
---

# 개떡찰떡 (Messy-In, Clean-Prompt-Out)

Take a disorganized idea-dump and produce **one** clean, structured prompt the user can paste into any LLM.

Core principle: **개떡같이 말해도 찰떡같이 알아먹는다.** The whole value is inferring intent from messy input WITHOUT interrogating the user. Asking lots of questions kills the point. Guess well, confirm lightly.

## Workflow

### 1. Extract intent
Read the whole dump. Find the real goal buried in the rambling. Strip tangents, repetitions, thinking-out-loud. State the goal to yourself in one line: "User actually wants X."

### 2. Infer the gaps (don't ask)
Most idea-dumps leave things unstated. Fill them with the **most likely** value based on context — do NOT stop to ask. Infer:
- **Target / audience** — who reads or uses the output?
- **Format** — list, essay, code, table, email, etc.
- **Length / depth** — quick vs thorough?
- **Tone / register** — formal, casual, technical?
- **Constraints** — language, must-haves, must-avoids?
- **Role** — what expert persona should the LLM adopt?
- **Target LLM** — where will this prompt be pasted? Named tool ("챗지피티", "Gemini") → that. Unstated → assume Claude in the current environment.

When the dump genuinely under-specifies something, pick a sensible default and surface it as an assumption (see step 5). Default to the user's input language for the output prompt.

### 3. Plan a skill combo (Claude targets only)
If the target LLM is **not** Claude (ChatGPT, Gemini, etc.), skip this step — those can't run Claude skills.

If the target is Claude (Claude Code / Cowork / claude.ai), look at the **available skills list already in your context** and wire the useful ones into the prompt:

- **Use only skills that appear in that list**, with the exact name as listed (e.g. `frontend-design:frontend-design`, `code-review`). Never invent a skill. No list visible → skip and note it in assumptions.
- **Map skills to phases** of the task: plan → build → verify → ship. One skill per phase; if two overlap, keep the one whose description fits better.
- **Keep it to 1–4 steps.** Only skills that clearly earn their place. Simple task with no matching skill → no skill plan at all.
- **Include mandatory prerequisites.** If a skill's description says it MUST be loaded before some tool (e.g. `figma:figma-use` before `use_figma`), add it right before the step that needs it.
- **Skip mode/meta skills** (output-style modes, loop/schedule, gaeddeok itself) unless the dump actually asks for them.
- **State the handoff**: what each step produces for the next one.

### 4. Build the prompt
Assemble a single clean prompt using this structure (omit any part that doesn't apply — don't pad):

```
[Role] You are <persona/expertise>.
[Context] <relevant background the LLM needs>
[Task] <the concrete ask, imperative>
[Skill plan] <only from step 3: numbered steps "N. /<skill> — what it does here → output for next step">
[Format] <output shape>
[Constraints] <length, tone, language, must/must-not>
```

Write it as natural prose or labeled sections — whichever fits the task. Keep it tight. No filler. The prompt should be directly pasteable.

### 5. Output: prompt + assumptions + confirm
Return exactly three things, in this order:

1. **The cleaned prompt** — in a code block so it's copy-ready.
2. **What I assumed** — a short bullet list of the gaps you filled (e.g. "Assumed target = beginners", "Assumed Korean output"). This is how the user catches wrong guesses without you having to ask upfront.
3. **One-line confirm** — "이대로 OK? 틀린 가정 있으면 말해줘." (match the user's language.)

Do NOT open with clarifying questions. Guess, deliver, let them correct. Only ask a question if the dump is so empty that no reasonable guess is possible (rare — and even then, ask at most one).

## Examples

**Example 1 — vague feature dump**

Input:
> 어 그러니까 우리 앱에 알림 기능 같은거 넣고싶은데 사용자가 너무 많이 받으면 짜증날거같고 근데 중요한건 놓치면 안되고 뭔가 똑똑하게 보내는 그런거 챗지피티한테 물어볼 프롬프트 만들어줘

Cleaned prompt:
```
You are a senior product manager specializing in mobile notification UX.

Context: We have a mobile app and want to add a notification system. The
key tension: users get annoyed by too many notifications, but must not
miss important ones.

Task: Design a "smart" notification strategy that maximizes signal and
minimizes noise. Cover: how to prioritize/tier notifications, batching and
quiet-hours logic, personalization signals, and how to let users tune
frequency.

Format: Structured recommendations with brief rationale for each.
```
What I assumed:
- Output prompt in English (target = ChatGPT). Switch to Korean if you prefer.
- Persona = product manager (you said "smart sending" → strategy, not code).

이대로 OK? 틀린 가정 있으면 말해줘.

**Example 2 — half-formed writing request**

Input:
> 블로그 글 써야되는데 주제가 뭐 재택근무 그런거 장단점? 근데 너무 딱딱하지 않게 그냥 프롬프트만

Cleaned prompt:
```
당신은 일과 삶의 균형을 다루는 친근한 블로그 작가입니다.

주제: 재택근무의 장점과 단점.

작업: 재택근무의 장단점을 균형 있게 다루는 블로그 글을 작성하세요.
실제 사례나 공감 가는 상황을 곁들여 독자가 자기 일처럼 느끼게 하세요.

형식: 도입–장점–단점–마무리 구성, 800~1200자.

톤: 딱딱하지 않게, 대화하듯 편안하게.
```
What I assumed:
- Korean output, ~800–1200자 길이.
- Casual/conversational tone (you said "딱딱하지 않게").

이대로 OK? 틀린 가정 있으면 말해줘.

**Example 3 — Claude target, skill combo**

(Skill names below are illustrative. Always use the names in *your* context's skill list.)

Input:
> 우리 서비스 랜딩페이지 하나 만들어야되는데 이쁘게 그리고 접근성도 신경써야되고 다 만들면 리뷰도 한번 돌리고 싶어 클로드코드한테 시킬거

Cleaned prompt:
```
너는 시니어 프론트엔드 개발자다.

작업: 우리 서비스의 랜딩 페이지를 만든다. 히어로–기능 소개–CTA 구성.

스킬 플랜 (순서대로 사용):
1. /frontend-design:frontend-design — 디자인 방향을 정하고 페이지 구현 → 완성된 페이지 코드
2. /design:accessibility-review — 1의 결과를 WCAG 2.1 AA 기준으로 점검하고 지적 사항 수정
3. /code-review — 최종 diff의 버그 검토

형식: 단계마다 무엇을 했는지 2~3줄 요약 후 다음 단계로.
제약: 새 의존성 추가 최소화.
```
What I assumed:
- 대상 = Claude Code (네가 말함) → 설치된 스킬로 플랜 구성.
- 섹션 구성 = 히어로/기능/CTA (일반적인 랜딩 구조).
- 다른 환경에 붙여넣으면 스킬 이름이 다를 수 있음.

이대로 OK? 틀린 가정 있으면 말해줘.

## Notes
- Length of the prompt scales to the task. Simple ask → short prompt. Don't inflate.
- If the user later corrects an assumption, regenerate the full prompt — don't just patch one line in chat.
- This skill outputs a prompt, not the answer to the prompt. Don't accidentally execute the task yourself unless the user asks.
