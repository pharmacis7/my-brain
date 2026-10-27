# Notes formatting rules (Obsidian)

## Writing notes for a topic
When I give a topic name and point to a file, write notes on that topic into that file. The notes must be:
- **Obsidian friendly**: use callouts (`> [!note]`, `> [!tip]`, `> [!warning]`), mermaid diagrams, tables, `==highlights==` and code blocks where they help.
- **Short**: no filler or long paragraphs. Use bullets and small examples.
- **Interview-level depth**: cover exactly what is needed to crack elite SDE 1 interviews, and no more.
  - Include: core concepts, how things work, the common traps and "what does this print?" questions, and best practices.
  - Skip: rare edge cases, JVM flags, preview or future features, long API lists, and trivia that interviewers almost never ask.
  - If unsure whether something belongs, ask: "Would an SDE 1 interviewer realistically ask this?" If not, leave it out.
- **Easy language**: simple words and short sentences, with every term explained the first time it shows up.
- **Easy to visualize**: show ideas as pictures where possible, such as mermaid flowcharts, sequence diagrams, ASCII trees for folder layouts, and tables for comparisons.
- **Easy to read**: clear headings, short sections, bold for key words, `---` between big sections, and nothing longer than a few lines without a break.

## Headings
- Do not write a main `# Title` heading at the top of a note. Obsidian already shows the file name as the title, so a `# Title` that repeats it is a duplicate.
- Start the note directly with the sections. The top-level sections use `#` (what would have been `##`).
- Shift every deeper level up by one too: `###` → `##`, `####` → `###`, so the hierarchy has no gaps.
- `#` lines inside code blocks (shell comments etc.) are not headings — leave them as they are.
