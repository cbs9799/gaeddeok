---
name: gaeddeok-idea
description: Turn a messy, rambling brain-dump into a clean, structured idea brief that a HUMAN reads — core concept, goal, key components, structure, and open questions. Use whenever the user off-loads a half-formed idea and wants their own thinking organized (not a prompt) — phrases like "아이디어 정리해줘", "생각 좀 정리", "이거 정리해줘", "두서없는데 정리", "organize my idea", "help me structure this". Trigger when the user is clearly thinking out loud and wants clarity on the idea itself, rather than a prompt to feed an AI. If they want an LLM-ready prompt instead, use the gaeddeok-prompt skill.
---

# 개떡찰떡 — 아이디어 정리 (Idea Organizer)

Take a disorganized brain-dump and produce a **clean structured idea brief** the user reads to clarify their own thinking. This is NOT a prompt — output is for the human, not for pasting into an AI.

Core principle: **개떡같이 말해도 찰떡같이 알아먹는다.** Infer structure from chaos without interrogating. Guess the shape, surface assumptions, let them correct.

## What this produces vs. the prompt skill
- **gaeddeok-idea (this skill)** → organized thinking FOR THE HUMAN: what the idea is, why, what it's made of, what's still unclear.
- **gaeddeok-prompt** → an LLM-ready prompt FOR A MACHINE.
If the user wants something to paste into ChatGPT/Claude, switch to gaeddeok-prompt.

## Workflow

### 1. Find the core
Read the whole dump. Strip tangents and repetition. Identify the single core idea in one sentence: "핵심은 X." If there are multiple ideas tangled together, separate them.

### 2. Infer the skeleton (don't interrogate)
Pull out, inferring sensible values where the user left gaps:
- **목표 (Goal)** — what they're trying to achieve / what problem it solves.
- **핵심 구성요소 (Components)** — the distinct parts/features/sections the idea contains.
- **흐름·구조 (Structure)** — natural ordering, dependencies, phases.
- **전제·맥락 (Context)** — who it's for, constraints implied.
- **열린 질문 (Open questions)** — what's genuinely undecided and worth resolving next.

### 3. Output format
Use this exact structure (drop a section only if truly empty). Match the user's language.

```
## 핵심 한 줄
<one-sentence summary of the idea>

## 목표
<what success looks like / problem solved>

## 핵심 구성요소
- <component 1 — short description>
- <component 2 — ...>

## 구조 / 흐름
<ordered steps, phases, or how the parts relate>

## 가정한 것
- <gap you filled in, so the user can catch wrong guesses>

## 다음에 정할 것 (열린 질문)
- <undecided point worth resolving>
```

End with one line: "이대로 방향 맞아? 틀린 부분 짚어줘." (match user's language.)

Do NOT ask clarifying questions before producing the brief. Guess, deliver, surface assumptions, let them correct. Only ask if the dump is so empty no structure can be inferred (rare; then one question max).

## Example

Input:
> 어 그 동네 사람들끼리 남는 음식 나누는 그런거 하고싶은데 앱인지 단톡인지도 모르겠고 근데 음식 버려지는거 아깝잖아 그리고 모르는 사람한테 음식 받는게 좀 그럴수도 있고 신뢰 그런게 중요할거같고 정리좀

Output:
```
## 핵심 한 줄
동네 이웃끼리 남는 음식을 나눠 음식물 낭비를 줄이는 지역 기반 음식 공유 서비스.

## 목표
버려지는 음식 줄이기 + 이웃 간 나눔 활성화.

## 핵심 구성요소
- 음식 등록/요청 (남는 음식 올리고 받기)
- 지역 기반 매칭 (가까운 이웃끼리)
- 신뢰 장치 (평점·인증·후기 — 모르는 사람 음식 거부감 해소)

## 구조 / 흐름
등록 → 이웃에게 노출 → 요청·수락 → 직접 전달 → 후기로 신뢰 축적.

## 가정한 것
- 플랫폼 형태는 미정으로 두고 "서비스" 수준으로 정리함 (앱/단톡 선택은 다음 단계).
- 무료 나눔 전제 (판매 언급 없었음).

## 다음에 정할 것 (열린 질문)
- 앱 vs 단톡방 vs 웹 — 어느 형태로 시작?
- 신뢰 장치를 어디까지 (실명 인증? 평점만?)
- 음식 안전·책임 문제 어떻게 다룰지?
```
이대로 방향 맞아? 틀린 부분 짚어줘.

## Notes
- Brief length scales to idea complexity. Small idea → short brief. Don't pad.
- If the user corrects an assumption, regenerate the brief, don't patch one line.
- This organizes the idea; it does not execute it or build the thing.
