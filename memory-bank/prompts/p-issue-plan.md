# Write implementation plan
Generate a **commit-sized checklist** that is detailed enough for a junior developer to implement without opening the outline again.

## 1. Granularity rules
1. Every **top-level checkbox** must correspond to one Git commit:
   - Touches either **one file** *or* a tight cluster of ≤ 3 related files.
   - Adds/edits roughly ≤ 15 LOC per file (ignore for large config or data files).
2. Add **sub-checkboxes** for fine detail:
   - Spell out each specific insertion, deletion, or edit (e.g., “+ `MY_CONST` constant at line 45 of `constants.ts`”).
   - Include any literal strings, labels, or UI text being added so the dev can copy-paste.
3. Keep every box actionable: after ticking it, the codebase must compile and tests must pass.

## 2. Context-retention rules
- Quote *only* the necessary snippet of code or JSON being added/edited—avoid full files.

## 3. Description rules
- Begin with a **concise task title** (`### …`), then a 1-sentence summary.
- Inside each checkbox, start with a strong verb (“Add”, “Insert”, “Create”, “Register”…).
- After the verb, give a short *why* (“to enable filtering”, “so users can toggle visibility”).
- Use simple, direct sentences; avoid fluff.

## 4. Mandatory footer
Finish with
`- [ ] Write or update tests`
(This must always be the last checkbox.)

## 5. Output format
```md
### <Concise title>

<1-sentence summary>

- [ ] **Top-level change**
      Why it matters.
      - [ ] Sub-change A with line/file reference and literal content
      - [ ] Sub-change B …
- [ ] **Next top-level change**
      …
- [ ] Write or update tests
      Describe how to test the change.
```