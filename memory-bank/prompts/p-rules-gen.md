You are an expert software archeologist.

Produce a Markdown file of concise coding rules that agents inside this repository will follow.

### Process
1. **Scan** the codebase.
2. **Collect**:
   • Tech stack: languages, main framework (with major version), build tool, package manager, key libs.
   • Package scripts: `dev`, `build`, `lint`, `test`, **and** how to run a single test.
   • Style rules NOT autofixed by linters or formatting tools.
   • Naming conventions
   • Architectural layout (folder roles, shared layers).
   • Any existing rules from `.cursor/rules/`, `.cursorrules`, `.github/copilot-instructions.md`.
3. **Distill** prescriptive rules:
   • Sections **in this order** → *Build & Scripts*, *Tech Stack*, *Style*, *Naming*, *Architecture*, *Misc*.
   • Imperative voice; ≤ 110 chars per rule; ≤ 6 rules per section.
   • Skip pure-formatter trivia (indent size, quote marks, semicolons).
4. **File naming**:
   • If a `memory-bank/CODING_RULES.md` already exists, improve it
   • Else create `memory-bank/CODING_RULES.md`.
5. **Header**: First line must be `# Coding Rules
