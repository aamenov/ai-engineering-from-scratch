---

name: check-current-lesson
version: 1.0.0
description: Quiz only the currently opened lesson from AI Engineering from Scratch. Trigger with "quiz current lesson", "test this lesson", "check this lesson", or `/check-current-lesson`.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Check Current Lesson

Test your knowledge of the currently opened lesson from the AI Engineering from Scratch course.

This skill quizzes ONLY one lesson at a time, using that lesson's `docs/en.md` file as the source of truth.

## Activation

This skill activates when the user says things like:

* `/check-current-lesson`
* "quiz current lesson"
* "quiz me on this lesson"
* "test this lesson"
* "check this lesson"
* "check my understanding of this lesson"
* "do I understand this lesson?"
* "am I ready for the next lesson?"

## Input

This skill does not require a phase number.

It should infer the current lesson from the currently opened file or current working directory.

Examples:

If the current file is:

```text
phases/00-setup-and-tooling/01-dev-environment/code/verify.py
```

then the lesson docs are:

```text
phases/00-setup-and-tooling/01-dev-environment/docs/en.md
```

If the current file is:

```text
phases/00-setup-and-tooling/01-dev-environment/docs/en.md
```

then use that file directly.

The user may also provide a lesson path manually, for example:

```text
/check-current-lesson phases/00-setup-and-tooling/01-dev-environment
```

## Procedure

### Step 1: Resolve the Current Lesson

Determine the current lesson directory.

A lesson directory has this structure:

```text
phases/<phase-dir>/<lesson-dir>/
├── code/
├── docs/
│   └── en.md
└── outputs/
```

Find the closest parent directory that contains:

```text
docs/en.md
```

If the lesson cannot be found, say:

```text
I could not find the current lesson docs. Please open a file inside a lesson folder or provide the lesson path manually, for example:
phases/00-setup-and-tooling/01-dev-environment
```

### Step 2: Read Only the Current Lesson

Read only this file:

```text
<current-lesson-directory>/docs/en.md
```

Do not read the whole phase.

Do not use general knowledge.

Do not generate questions from other lessons.

All questions must be directly grounded in the current lesson content.

If `docs/en.md` does not exist, say:

```text
This lesson does not have docs/en.md, so I cannot quiz you on it yet.
```

### Step 3: Generate 8 Questions

Create exactly 8 multiple-choice questions from the current lesson.

Question types:

**Questions 1-4: Conceptual**

These test understanding of ideas, definitions, and reasoning.

Examples:

* What problem does this lesson solve?
* Why is this tool or concept useful?
* Which statement best describes the purpose of X?
* What is the relationship between A and B?

**Questions 5-8: Practical**

These test applied knowledge and implementation awareness.

Examples:

* Which command correctly performs X?
* What should you do if Y fails?
* What is the correct order of steps?
* Which file should you open or modify?

Each question must have 3 or 4 answer options labeled A, B, C, and optionally D.

Exactly one option must be correct.

Wrong answers should be plausible but clearly wrong to someone who studied the lesson.

### Step 4: Ask One Question at a Time

Ask only one question at a time.

Format:

```text
Question 1/8 (Conceptual) — from current lesson

[Question]

A) ...
B) ...
C) ...
D) ...
```

Wait for the user's answer before continuing.

Do not show the correct answer before the user responds.

### Step 5: Grade Each Answer

After the user answers:

* Say whether the answer is correct or incorrect.
* If incorrect, give the correct answer.
* Explain the reason in 1-2 simple sentences.
* Continue to the next question.

### Step 6: Track the Score

Keep a running tally:

* Correct answers out of 8
* Missed question numbers
* User's wrong answers
* Correct answers
* Topic to review

### Step 7: Final Result

After all 8 questions, display the score and grade:

**7-8 correct: Mastered**

```text
You have a strong grasp of this lesson. You are ready to move on to the next lesson.
```

**5-6 correct: Almost**

```text
Solid foundation. Review the missed points before moving on.
```

**3-4 correct: Developing**

```text
You are building understanding, but you should reread this lesson and rerun the code.
```

**0-2 correct: Start Over**

```text
This lesson needs more time. Read docs/en.md again, run the code, and then retake the quiz.
```

### Step 8: Wrong Answer Breakdown

For every missed question, show:

```text
Question N: [short version of question]
Your answer: B
Correct answer: C — [correct option text]
Why: [1-2 sentence explanation]
Review: <current-lesson-directory>/docs/en.md
```

### Step 9: What Next?

End by offering three choices:

1. **Retake this lesson quiz** — generate a fresh set of 8 questions from the same lesson.
2. **Try another lesson** — the user opens another lesson or provides another lesson path.
3. **Explain a missed topic** — explain any concept from the questions they missed.

Wait for the user's choice.

## Rules

* Quiz only the current lesson.
* Use only the current lesson's `docs/en.md`.
* Do not quiz the whole phase.
* Do not use other lessons unless the user explicitly asks.
* Ask exactly 8 questions.
* Ask one question at a time.
* Do not reveal the correct answer before the user answers.
* Keep question text concise.
* Wrong options must be plausible.
* No joke answers.
* If the lesson content is too short for 8 strong questions, say so and create as many high-quality questions as possible.
* Prefer practical questions that help the user actually use the repo.
