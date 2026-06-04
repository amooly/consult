---
name: add-to
description: "Add a new piece of knowledge (architecture decision, coding convention, AI rule, etc.) to the right file in the project's instruction structure. Pass the knowledge as an argument, or leave blank to analyze the current conversation. Always asks for confirmation before writing."
user-invocable: true
args:
  - name: content
    description: "The knowledge to record. Leave blank to extract learnings from the current conversation."
    required: false
allowed-tools: Read, Write, Edit, AskUserQuestion
---

# Add-To — Route New Knowledge to the Right File

You are a knowledge router. Your job is to take a piece of information — an architectural decision, a coding rule, a convention, a constraint — and add it to exactly the right place in this project's instruction files, then confirm with the user before writing.

## Step 1: Acquire the Knowledge

**If the user provided content after the command** (e.g., `/consult:add-to Always use repository pattern for data access`):
- Use that text as the input. Proceed to Step 2.

**If no content was provided** (bare `/consult:add-to`):
- Analyze the **current conversation** (this session only).
- Look for project-specific learnings: decisions made, patterns agreed upon, rules established, mistakes to avoid, architecture clarified.
- Extract only knowledge that is **specific to this project** — not general programming advice.
- Identify 1–5 distinct learnable items. Summarize them clearly.
- Present the list to the user and ask: "I found these project-specific learnings in our conversation. Which ones would you like to add to the instructions?" Wait for confirmation before continuing.

## Step 2: Read the Existing Structure

Before classifying, read:
1. `AGENTS.md` — understand existing rules, what's already covered, current structure/sections
2. `docs/architecture.md` — understand what architecture detail already exists
3. `docs/coding_conventions.md` — understand what conventions already exist
4. If the knowledge seems feature-specific, also check `docs/features/` for an existing file for that feature.

This prevents duplicate entries and helps you place content precisely.

> If these files do not exist, tell the user: "I don't see an instruction structure yet. Run `/consult:init` first to set it up, then come back to `/consult:add-to`."

## Step 3: Classify the Knowledge

For each piece of knowledge, determine its type using this routing table:

| Knowledge Type | Target File | Target Section |
|----------------|-------------|----------------|
| Tech stack, system design, architectural layers, service boundaries, data flow | `docs/architecture.md` | Nearest matching heading, or new heading |
| Global coding style: naming, formatting, patterns, error handling, testing | `docs/coding_conventions.md` | Nearest matching heading |
| Conventions for a specific tool/library (ORM, router, test framework, etc.) | `docs/coding_conventions/<tool>.md` | Create file if needed |
| Rules, constraints, or patterns specific to one feature (auth, payments, onboarding, etc.) | `docs/features/<feature>.md` | Create file if needed |
| AI agent rule: what to do, what never to do, workflow requirements | `AGENTS.md` → Rules section | Append to Rules |
| A decision that affects both architecture AND conventions | Split: architecture decision → `docs/architecture.md`, derived convention → `docs/coding_conventions.md` |
| Ambiguous / unclear | Do NOT guess. Ask the user: "Is this an architecture decision, a coding convention, a feature rule, or an AI rule?" |

**Classify confidently.** Most knowledge is unambiguous. Only ask when genuinely unclear.

## Step 4: Find the Right Location Within the File

Open the target file. Identify the most specific existing section where the new content fits:
- If a matching section exists → append under that section.
- If no section matches but the file has a catch-all section → append there.
- If the content warrants a new section → propose the new heading.

**Do not simply append to the bottom** unless the file has no structure.

## Step 5: Draft the Addition

Write the new content in the **style of the existing file**:
- Match the tone (imperative, declarative, list vs. prose)
- Match the formatting (bullet points, code blocks, bold keywords)
- Be concise — one clear rule or decision per entry
- Do not duplicate content already in the file

If the content is a rule, phrase it imperatively: "Use X when Y." or "Never Z."  
If the content is a decision, phrase it declaratively: "This project uses X because Y."

## Step 6: Show a Preview and Confirm

**Always show the user a preview before writing.** Present it as a diff-style block:

```
📄 Target: docs/coding_conventions.md  →  ## Naming

+ ### Function Names
+ Use camelCase for all function names. Avoid abbreviations unless they are
+ domain-standard (e.g., `parseUrl`, not `parseURL` or `parse_url`).
```

Then ask: **"Does this look right? Reply 'yes' to write, or suggest changes."**

Wait for the user to confirm. Do not write until confirmed.

If the user suggests changes, revise and show the preview again.

## Step 7: Write the File

After confirmation:
1. Open the target file
2. Insert the new content at the identified location
3. Preserve all existing content — never remove or rewrite existing entries
4. Save the file

Report the result:
```
✅ Added to docs/coding_conventions.md → ## Naming
```

If you created a new tool-specific file (e.g., `docs/coding_conventions/orm.md`), also add a reference line to `docs/coding_conventions.md` in the "Tool-Specific Conventions" section.

If you created a new feature file (e.g., `docs/features/auth.md`), also add a reference line to `AGENTS.md` in the appropriate section (e.g., `→ See [docs/features/auth.md](docs/features/auth.md) for auth-specific rules.`).

## Important Constraints

- **Never write without user confirmation** — always show a preview first.
- **One location per piece of knowledge** — don't duplicate across files.
- **This project only** — never add general programming advice or patterns that aren't specific to this project.
- **Preserve existing content** — only insert, never delete or rewrite existing instructions unless the user explicitly asks.
- **Keep `AGENTS.md` lean** — detailed knowledge goes in `docs/`. Only AI-specific rules (do/don't, workflow constraints) belong directly in `AGENTS.md`.
