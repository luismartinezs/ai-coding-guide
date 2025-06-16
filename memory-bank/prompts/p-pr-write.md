**Objective:** Generate a well-structured pull request using provided context and PR template.
**Inputs Provided:**
1. Code diff for the target pull request
2. Additional task context (e.g. task outline)
3. PR template to adhere to

**Steps:**
1. Analyze diff: Review the code changes (additions, deletions, modifications) from the provided diff of the pull request to understand the scope and purpose of the changes
2. Retrieve task context: The task context is provided in the memory-bank/issues/[issue_number] folder (replace [issue_number] with the actual issue number I provided)
3. Review task context: Review the additional task context to identify the problem solved, requirements met, or feature added, and use this to inform the PR content
4. Structure the PR: Follow the provided PR template EXACTLY to write the PR.

**Key rules:**
- Write the PR prioritizing clarity and readability, guide the reviewer to facilitate their review, providing context and reasoning to understand the changes
- Omit irrelevant, empty or not applicable sections in the PR body
- If you are unable to complete a section of the PR, mark it as "> TODO" and add a short comment describing what to do. E.g.: "> TODO: Add a screenshot of the new feature"
- Rely ONLY on the diff to understand what was actually done. Use the task context provided ONLY to inform the goal, motivation and reasoning of the changes.