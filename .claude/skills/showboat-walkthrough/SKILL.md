---
name: showboat-walkthrough
description: >
  Build executable code walkthrough documents using the showboat CLI tool.
  Use this skill when the user asks to "write a walkthrough", "explain this codebase",
  "create documentation with showboat", "document how the code works", "code walkthrough",
  "trace the code path", or wants a linear explanation of how a system works with
  embedded code snippets and live output. Also trigger when the user mentions "showboat"
  in the context of documentation. This skill covers the full workflow: codebase exploration,
  section planning, showboat document construction, and verification.
---

# Showboat Walkthrough Skill

Build a walkthrough.md (or similar) that mixes narrative commentary with executable code
blocks and captured output. The document is both readable documentation and reproducible
proof: `uvx showboat verify walkthrough.md` re-runs every code block and diffs the output.

## Showboat CLI Reference

```
showboat init <file> <title>           # Create new document
showboat note <file> [text]            # Append commentary (markdown)
showboat exec <file> <lang> [code]     # Run code, capture output, append both
showboat pop <file>                    # Remove most recent entry (undo)
showboat image <file> <path>           # Embed an image
showboat verify <file>                 # Re-run all code blocks, diff output
```

Install: `uvx showboat` (runs via uvx, no install needed).

Key behaviors:
- `exec` prints captured output to stdout AND appends to the document
- `exec` exits with the command's exit code. Use `pop` to remove failed entries.
- `note` accepts text as an argument or via stdin
- `verify` re-runs every exec block and reports diffs (exit 1 if any changed)

## Workflow

### Phase 1: Explore Before Writing

Do not start writing until you understand the full system. Use the Explore agent (or
direct Glob/Grep/Read) to map every file that matters. For each file, note:
- What it does (one sentence)
- Key classes/functions
- How it connects to other files
- The data flow through it

If the user specifies a "best" or "default" path through the code (e.g., "use the
S4 model as the guide"), trace that path end-to-end before writing anything.

### Phase 2: Plan Sections

Organize sections by **data flow or signal path**, not by file or alphabetical order.
The reader should be able to follow one request from input to output.

Typical ordering for a training system:
1. Configuration (what gets loaded)
2. Entry point (how it starts)
3. Data pipeline (how input is prepared)
4. Model construction (what gets built)
5. Forward pass (signal flow through the model)
6. Loss computation (how error is measured)
7. Optimizer setup (how learning happens)
8. Supporting infrastructure (logging, callbacks, etc.)

Typical ordering for a web service:
1. Entry point / routing
2. Request handling
3. Business logic / core modules
4. Data layer (DB, cache, external APIs)
5. Response formatting
6. Error handling / middleware

Adapt to the actual system. The principle: follow the data, not the directory tree.

### Phase 3: Build the Document

Start with `showboat init`, then alternate `note` and `exec` blocks.

**Commentary blocks** (`note`): Explain what comes next, why it matters, and how it
connects to what came before. Keep these focused. A good note:
- Sets up the context for the code that follows
- Points out the non-obvious (not "here is a function that adds two numbers")
- Links back to earlier sections when relevant
- Uses markdown formatting (headers, tables, code fences for inline examples)

**Code blocks** (`exec`): Show real code from the repo. Every exec block must produce
deterministic output that will survive `verify`. Rules:

1. **Use `sed -n 'start,endp'` for targeted excerpts.** This is the workhorse. Pick
   line ranges that show exactly the relevant code without noise.

2. **Use `cat` only for short files** (under ~80 lines). For anything longer, use sed
   to extract the relevant section.

3. **Never use commands with non-deterministic output** in exec blocks: timestamps,
   PIDs, memory addresses, GPU stats, random numbers, file modification times. These
   will break `verify`.

4. **Live verification is powerful.** If the walkthrough claims "28k parameters", run
   a script that instantiates the model and prints the count. This proves the claim
   and catches documentation rot.

5. **Use `grep -n` to show where things are** before diving into sed ranges. Example:
   `grep -n 'class MyModel' src/model.py` shows the reader where to find it.

6. **Handle failures with `pop`.** If an exec block fails or produces unwanted output,
   immediately `showboat pop` and retry with a corrected command.

**Section flow pattern:**
```
note: "## Section Title\n\nContext about what we're looking at and why."
exec: grep or sed showing the relevant code
note: "Explanation of what the code does, key decisions, connections to other parts."
exec: (optional) live verification or a different angle on the same code
note: "Bridge to the next section."
```

### Phase 4: Quality Checks

Before declaring the walkthrough complete:

1. **Check structure**: `grep "^## " walkthrough.md` to verify section headings make sense.
2. **Check length**: `wc -l walkthrough.md` to gauge completeness.
3. **Verify claims**: Any numerical claim (parameter counts, dimensions, performance
   numbers) should have a corresponding exec block that proves it.
4. **Run verify**: `uvx showboat verify walkthrough.md` to confirm all exec blocks
   still produce the same output.

## Common Patterns

### Showing a function with context
```bash
showboat exec walkthrough.md bash "sed -n '42,78p' src/model.py"
```

### Showing where something is defined
```bash
showboat exec walkthrough.md bash "grep -n 'class Trainer' src/training/*.py"
```

### Live parameter count
```bash
showboat exec walkthrough.md bash "uv run python -c \"
from src.model import MyModel
m = MyModel()
print(f'Parameters: {sum(p.numel() for p in m.parameters()):,}')
\""
```

### Showing config files (usually short enough for cat)
```bash
showboat exec walkthrough.md bash "cat configs/default.yaml"
```

### Multi-line notes
```bash
showboat note walkthrough.md 'First paragraph.

Second paragraph with **bold** and `code`.

| Column A | Column B |
|----------|----------|
| value    | value    |'
```

## Pitfalls

- **Don't exec commands that modify state** (create files, install packages). The
  walkthrough should be read-only. `verify` will re-run everything.
- **Don't use `head` or `tail` on large files** in exec blocks -- the output might
  change as the file grows. Use `sed -n` with explicit line numbers.
- **Escape quotes carefully** in exec blocks. Single-quote the outer argument, use
  escaped double quotes inside, or use heredoc-style Python strings.
- **Don't over-annotate.** Not every line of code needs explanation. Focus on the
  architecture, the decisions, and the non-obvious. Let well-written code speak.
- **Watch for Windows path issues.** On Windows with Git Bash, use forward slashes
  and be aware that sed/grep behave slightly differently than on Linux.
