---
name: document-a-feature
description: >-
  Creates self-contained Markdown flow docs with an HTML nested unit diagram,
  file list, and where-else notes. Use for frontend or backend when the user
  asks to document a flow, create a flow .md, explain how a feature works
  end-to-end, or map which modules/files a path uses.
disable-model-invocation: true
---

# Document a Feature

Write a short `.md` that explains one code flow so a human can skim it fast.
Works for **frontend** and **backend**. Language- and framework-agnostic.

---

## When to use

User asks to document a flow such as:

- “Document the [X] flow”
- “Create a .md for how X works”
- “Explain this flow and which files are involved”
- “Create a flow diagram for [FLOW NAME / PATH]”

---

## Step 1 — Gather information

Do not invent the flow. Trace the real code first.

1. **Find the entry**
   - Frontend: screen, page, route, button, command the user triggers; note auth/role gates.
   - Backend: route/handler, job, consumer, CLI entry, public API method; note auth/guards.

2. **Walk the real call / composition tree**
   - Start at the entry unit → each unit it owns or calls for this flow → leaf.
   - A **unit** is one real **project-owned** file-backed piece: component, module, class, service, repository, hook, middleware, etc.
   - Include optional / conditional **project** units (do not skip).
   - **Skip system defaults** (see below) — do not put them in the diagram or Files list.
   - Note edge type: **renders / composes** vs **invokes / calls**, plus real syntax from the codebase.

3. **Trace the primary action**
   - User or system trigger → handler → nested work → side effects (persist, emit, HTTP) → return/callback.
   - Callbacks or results that return to a parent must **reuse** that parent box (do not draw it twice).

4. **Find every file involved**
   - Every **project** unit on the path, plus shared helpers/clients only if this flow uses them and they are project-owned.
   - Build the file list — every entry must appear in the diagram.
   - Never list language stdlib, framework primitives, or third-party UI/component-library widgets as flow units.

5. **Scope “where else”**
   - Search for the main unit’s other call sites; mark other entry paths as separate flows.

### Skip system defaults (do not diagram)

Do **not** show units that are language, platform, or library defaults. Examples:

- **UI / component libraries:** Button, Text, View, Stack, Modal shell from a design system, Icon wrappers, etc. (unless the project owns a meaningful wrapper that encodes flow logic)
- **Language / runtime builtins:** `map`, `filter`, `JSON.parse`, `Promise`, `setTimeout`, standard collections, etc.
- **Framework plumbing:** router outlet primitives, DI container internals, ORM base `save()` with no project wrapper, generic HTTP client with no project API module
- **Third-party SDK surface** used only as a dumb call (e.g. raw `fetch` / vendor SDK) when the project’s own client/module is the unit to show instead

**Do show** project modules that wrap those defaults when the wrap is part of the flow (e.g. `OrdersApi.create`, `useAppModal`, `PaymentService`).

If a parent only renders library chrome around a project child, keep the parent project unit and the project child — omit the library chrome as its own box.

Checklist:

```
- [ ] Entry point known (FE or BE)
- [ ] Unit tree walked (no skipped project optionals)
- [ ] System defaults / library primitives excluded
- [ ] Edge types noted with real syntax
- [ ] File list complete (project files only)
- [ ] Related-but-different flows identified
```

---

## Step 2 — Place and name the file

- Put the `.md` next to the main entry file for that flow (or the owning package/module).
- Name it after the flow, e.g. `CreateOrderFlow.md`, `PasswordResetFlow.md`.
- One flow per file.

---

## Step 3 — Document structure

```markdown
# [Flow name]

[Entry path] → **[Action]** (who/what can trigger it).

[Optional one sentence: what it does / creates. Main unit: `Name`.]

[HTML diagram — see Step 4]

---

## Files in this flow

[relative/path/File.ext](relative-link)

One-line description for this flow.

---

## Where else

[1–3 sentences: only used here vs separate related flows.]
```

### Nav path line

- Frontend: `Screen → Section → **Action** (role).`
- Backend: `API/Job → **Operation** (caller).`
- Adapt labels to the domain; keep one line.

### Files section rules

- **No tables.**
- Clickable relative link; blank line; one short description line.
- Order: entry → children/callees → shared infrastructure used on the path.

### Do not include

- ASCII box diagrams as the main diagram
- Mermaid
- Sequence / state / separate architecture diagrams unless the user asks
- Splitting one unit into fake stages (Load / Form / Fetch / Finish / Validate as separate boxes)
- System defaults: component-library widgets, language builtins, framework/SDK primitives (see Step 1)

---

## Step 4 — Flow diagram (required format)

Trace real code first. Then write a diagram that matches this format **exactly**.

### Output shape (diagram header)

1. Title: `# [Flow name]`
2. One-line path: entry → **action** (who/what triggers it).
4. Then an **HTML diagram** (not Mermaid, not ASCII boxes).

### Diagram rules

#### Units (boxes)

- One rectangle per real **project-owned** unit/file only.
- Never split one unit into stage boxes (Load / Form / Fetch / Finish / etc.).
- Nested ownership or call = next column to the right (`margin-left: 5rem` wrapping the child).
- Parent left, child right.
- Do not create boxes for system defaults (UI library components, utility functions, language builtins, framework plumbing, dumb third-party SDK calls).

#### Human sentence + code (required)

Every important detail in a **box** or on an **edge** must be easy for a human to read:

1. **Plain sentence first** — what happens in this flow (role, action, outcome).
2. **Then the real code** in short pseudo / call form (symbol, JSX, method).

Never show bare code alone as the only explanation.

| Bad (code only) | Good (sentence + code) |
|-----------------|------------------------|
| `&lt;TasksTab /&gt;` | Renders the tasks slot: `&lt;TasksTab /&gt;` |
| `openEditor(null)` | Opens the editor in create mode: `openEditor(null)` |
| `orderService.create(dto)` | Creates the order: `orderService.create(dto)` |
| `Renders / ChildView` | Composes the child list: `&lt;ChildView /&gt;` |

Apply this pattern to box detail lines **and** connecting-arrow labels.

#### Box content

- Top line: the main thing in `<strong>…</strong>` — component, class, function, module, or other primary unit name (normal size, bold).
- Directly under that: **file path** in small italic blue text:
  - `font-size: 0.8em; font-style: italic; color: #2563eb; line-height: 1.45`
  - Full path from repo/project root when practical (e.g. `screens/…/TasksTab.tsx`)
- Under the path, small text (`font-size: 0.8em; line-height: 1.45`):
  - One line (or short block) per important behavior: **human sentence**, then **code/pseudo**
  - Prefer `Sentence: code`
- Phrasing patterns (adapt to FE or BE):
  - "Shows the add control for owners only: `canAddItem`"
  - "Opens the editor in create mode: `openEditor(null)`"
  - "Handles create order requests: `OrdersController.create` (POST /orders)"
  - "Persists the order row: `OrderRepository.save`"
  - "Renders the tasks slot: `<TasksTab />`"
- Do NOT dump unrelated state, fields, or helpers. Only what matters for THIS flow.

#### Edges

- Between boxes: small-font edge labels.
- Same rule: **human sentence**, then **code/pseudo** — not code alone.
- Say whether it renders/composes or invokes/calls inside the sentence when helpful.
- Examples:
  - `Renders the tasks slot:` + `&lt;TasksTab /&gt;`
  - `User taps add; opens sheet in create mode:` + `openEditor(null)`
  - `Invokes order creation:` + `orderService.create(dto)`
  - `Saves the entity:` + `repo.save(entity)`
- Return paths / callbacks loop back to the same parent box — do not draw that unit twice.
- Callback edges also use sentence + code (e.g. `On save, refresh the list: handleSave()`).

#### HTML constraints (important)

- Use CSS `border: 1px solid currentColor` boxes (not ASCII `|` box borders for the unit).
- Use this styling pattern:

```html
<div style="font-family:ui-monospace,SFMono-Regular,Menlo,monospace;font-size:13px;line-height:1.35">
  <div style="border:1px solid currentColor;padding:8px 12px;max-width:22rem">
    <div><strong>UnitName</strong></div>
    <div style="font-size:0.8em;font-style:italic;color:#2563eb;line-height:1.45;margin-top:2px">path/to/File.ext</div>
    <div style="font-size:0.8em;line-height:1.45;margin-top:4px">Renders the tasks slot: &lt;TasksTab /&gt;<br>Opens editor in create mode: openEditor(null)</div>
  </div>
  <div style="padding:6px 0 6px 1rem;font-size:0.8em;line-height:1.45">│<br>│ Renders the tasks slot:<br>│ &lt;TasksTab /&gt;<br>└──────────────▶</div>
  <div style="margin-left:3rem">
    <!-- nested child box -->
  </div>
</div>
```

- Escape `<` `>` in Markdown HTML when showing tag-like syntax (`&lt;Child /&gt;`).
- For a return/callback edge beside a forward edge, place a second small label (e.g. flex row) pointing back to the parent — still one parent box only.

---

## Step 5 — Verify

- [ ] Title + path line + HTML diagram present
- [ ] One box per real project unit (no fake stage splits; no system defaults)
- [ ] Each box: **bold main name**, then **italic blue small file path**, then flow detail
- [ ] Box and edge text use **human sentence + code/pseudo** (no bare `&lt;X /&gt;` alone)
- [ ] Parent left → child right via `margin-left: 5rem`
- [ ] Returns/callbacks reuse parent (no duplicate parent box)
- [ ] No component-library / language-builtin / framework-default boxes, utility functions
- [ ] No Mermaid / ASCII-as-main-diagram
