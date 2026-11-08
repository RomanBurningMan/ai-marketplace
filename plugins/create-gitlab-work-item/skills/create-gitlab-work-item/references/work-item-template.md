# GitLab work item description template

This is the canonical GitLab work-item template. Use it to assemble the **description** field as
GitLab-Flavored Markdown. Every heading below is a real `#`/`##` heading in the body. Fill each
section from the user's brief; never leave a placeholder like `<ID>` or "Describe…" in a real work
item — either populate it or omit the line.

## Section reference

### `# Context`
Always present. The whole context of the work item: what we want to achieve and why. One or two short
paragraphs is usually enough. **Read primarily by Product Managers** — keep it in plain,
outcome-focused language and minimise code-level technicals (class names, namespaces, types, table
names). Put technical detail in the Implementation Plan instead. If a technical reference is truly
necessary here, format it as inline code (see "Formatting technicals" below).

### Incident-only subsections (include **only** when the work-item type is `Incident`)
These live under `# Context`, before `# Expected Result`. Omit all three for Issue / Task / Test case.

```
## Data

A list of initial data to ease the engineer's work. Include only the lines that apply — use whatever
identifiers are meaningful in the relevant domain. Generic examples:

* Account / Customer ID: <ID>
* Affected entity ID: <ID>      (e.g. the record, order, or item involved)
* Reference ID: <ID>            (if applicable)
* Reference URL: <URL>          (if applicable)

## Steps to Reproduce

A simple numbered list of steps that reproduce the problem.

## Actual Result

What actually happens — the behaviour considered a bug or otherwise invalid.
```

### `# Expected Result`
Always present. The result we want to achieve in this work item. For Incidents, this is the correct
behaviour that should replace the actual result. Like Context, this is **PM-facing** — describe the
observable outcome in plain language and keep technicals to a minimum; format any that remain as
inline code.

### `# Acceptance Criteria`
Always present. A **numbered list** of criteria (`1.`, `2.`, `3.` — never plain bullets), so
reviewers and QA can reference each one unambiguously ("AC 3"). Draft these from the brief, then let
the user refine them in the preview. Prefer several small, independently checkable criteria over one
big one.

**Shape of a criterion:** each numbered item holds **one full, coherent sentence** that states the
behaviour to verify — no bold keywords, no line breaks inside the item, no telegram-style fragments.
Fold a Given/When/Then ordering into the sentence when it reads naturally ("Given …, when …,
then …"); when it doesn't, write a plain sentence instead — a readable sentence always takes
precedence over the GIVEN/WHEN/THEN format. Example:

```
1. Given an account with no saved records, when the user opens the list view, an empty-state
   message is shown instead of a loading spinner.
2. The empty-state message links to the "create record" form.
```

In markdown this is an ordinary ordered list.

**What is not an acceptance criterion.** A criterion is something a reviewer or QA can verify against
the running system, written in plain language. These four keep appearing and none of them belong —
move each to the `# Implementation Plan`, which is where an implementer will look for them anyway:

| Not a criterion | Why | Where it goes |
|---|---|---|
| "Field names use camelCase" | A naming convention, not a behaviour | Implementation Plan |
| "The field is non-nullable, mirroring upstream" | A typing decision | Implementation Plan |
| "The summaries are grouped under a single section" | An internal structure choice | Implementation Plan |
| "The value uses the same scale as the other rates" *(while the scale is still an open question)* | Pre-commits the team to a decision nobody has made | Action Points, until answered |

That last one is the subtle one. A criterion that quietly assumes the outcome of an unresolved question
turns into a silent commitment: someone implements to satisfy it and, in doing so, decides something
that was supposed to be decided elsewhere. If a question is open, it belongs in `# Action Points`, not
smuggled into a criterion.

**Avoid type vocabulary.** Say what a consumer observes, not how it is represented. "The narrative
section is `null`" becomes *"no summary content is returned"*; "those fields are `null` for unmatched
rows" becomes *"no directory details are returned for that row"*. The precise representation — `null`
versus the key being absent — is real and worth writing down, but it belongs in the Implementation
Plan for the developer, not in the sign-off contract.

**Don't force a count.** Fewer criteria and less technical criteria are different goals. Keep every
distinct verifiable behaviour as its own criterion so a reviewer can point at one unambiguously;
merging two to shorten the list makes sign-off harder, not easier. A work item may legitimately end
with two criteria or with six.

**Sentry follow-up criterion (conditional):** if the work item references any Sentry issue(s) — a
link in the Incident `## Data` section, or mentioned in the brief — add a criterion that the issue
must be **Resolved** once the work is done. Name the specific issue when known. Example:

```
3. Once this work item is delivered, the linked Sentry issue (`PROJECT-BACKEND-1A2B`) is marked
   Resolved and stops recurring.
```

Omit this when no Sentry issue is connected to the work item.

**Published-documentation criterion (conditional):** if the work item changes a **documented or
published** interface — an API response payload, an endpoint, a CLI contract — add a criterion that
the published documentation is updated to reflect the endpoints or commands named in QA Notes.
Without it you ship fields consumers can't discover, because they read the published reference rather
than your code. Name the project's actual docs location (Stoplight, Swagger, a docs site, a README)
rather than assuming one, and delegate the specifics to QA Notes instead of restating every field, so
the criterion doesn't go stale when the field list changes. Example:

```
4. The Stoplight documentation has been updated to reflect the endpoints mentioned in the QA Notes.
```

Omit it when nothing published changes.

### `# Action Points`  (**only when the work item isn't fully specified** — after Acceptance Criteria)

Include this **only** when something must be answered before the work is buildable, and the answer
isn't ours to give — typically a contract detail owned by another team, or a product decision still
open. A **numbered list**, and unlike QA Notes and Implementation Plan it stays **visible**: an
unanswered blocking question that nobody sees is worse than no question at all.

What qualifies:

* an upstream field name, type, unit or scale that the upstream item left ambiguous
* a contradiction *within* the source material ("the summary says total checks, the criterion says
  checks with a response — which is intended?")
* a decision waiting on someone else (design, product, another team's work item)

Each point should say what to confirm, with whom, and why it matters — what changes depending on the
answer. "Confirm the field name" is weak; "Confirm the field name: the upstream item offers both
`is_branded` and `type`, and we can't map either until it's fixed" tells the reader why they can't
just start.

What does **not** qualify: work we could simply decide ourselves, or a reminder to do part of the
work item. If it's ours to choose, choose it in the Implementation Plan.

**Retiring them.** Action Points are temporary. When one is answered, fold the answer into the
Acceptance Criteria, QA Notes or Implementation Plan wherever it now belongs, then delete the point.
When all of them are answered, remove the whole section rather than leaving an empty heading. A work
item whose Action Points all sit there answered-in-a-comment is one nobody trusts.

### `# QA Notes`  (**only when relevant** — directly after Acceptance Criteria, or after Action Points)
Include this section **only** when the work item involves specific **API endpoints** or **console/CLI
commands**. Its job is to make a QA's testing easier by handing them the concrete entry points:

* API endpoints — method + path/URL, e.g. `` `POST /api/v2/reports/{id}/refresh` ``. Add a link if
  there's a relevant API doc or environment URL.
* Console / CLI commands — the command name **with its parameters/flags**, e.g.
  `` `bin/console app:report:regenerate --report-id=123 --force` ``. Use a fenced code block for a
  full command line.

Keep it concrete and copy-pasteable. Keep the visible `# QA Notes` **heading** and place the notes
inside a **collapsed `<details>` block** directly beneath it (see `gitlab-markdown.md`). If the work
item has no endpoints or commands to test through, **omit the section entirely** — never add an empty
or guessed QA Notes.

### `# Implementation Plan`  (heading + `<details>` — **only when a plan exists**)
Include this section **only** when the user provides or approves real implementation detail. If there
is no plan, omit the section entirely (do not emit an empty heading or block).

When present, keep the visible `# Implementation Plan` **heading**, then place the plan body inside a
**collapsed `<details>` block** directly beneath it — so the section is always visible but the detail
stays folded and the work item stays readable. See `gitlab-markdown.md` for the exact markup.

This is also the home for everything the Acceptance Criteria deliberately exclude — naming
conventions, types and nullability, internal structure — plus the reasoning behind a decision, so a
reviewer doesn't reopen it. Where a choice went against an obvious alternative, or against an existing
pattern in the codebase, say so and say why: "this deliberately diverges from X, which throws on an
unknown value; throwing is right there and wrong here because…".

**Provenance (when the plan rests on someone else's answer).** If the field-level detail came from
another team, open the plan by citing it: who confirmed it, when, a link to where, and whether it's
settled or provisional. For example:

```
Contract confirmed by <person> in the [#channel thread of <date>](<link>) (planned, not yet
implemented, so shapes may still move): the field is `is_branded`, a boolean, always present.
```

It earns its place twice over: a later reader can check the claim instead of trusting the work item,
and the settled-versus-provisional note tells the implementer whether to verify against a real
response first. When a stated premise turns out to be wrong, correct it in place and say it was wrong —
a plan that silently contradicts an earlier version invites the same mistake again.

## Formatting technicals

Whenever a code-level token appears in **any** section, wrap it so it reads unambiguously:

- Class names, namespaces, types, method names, field/column names, config keys → **inline code**
  with backticks (illustrative names only): `` `InvoiceCalculator` ``, `` `Billing\Tax\VatResolver` ``,
  `` `int` ``, `` `order_items` ``.
- Multi-line snippets (method signatures, SQL, config, JSON) → a fenced code block.
- Commit / MR / issue references → a **clickable link**, never a bare SHA as plain text. Use a
  Markdown link to the full commit URL with the short SHA as the text
  (`` [`f5a760db`](https://gitlab.com/<group>/<project>/-/commit/<full-sha>) ``), or GitLab's
  native references (`!42`, `#7`, `` project@<sha> ``) which auto-link in-project.

This matters most in Context / Expected Result (where a stray bare class name confuses a PM), but
apply it everywhere for consistency — including inside the Implementation Plan block.

## Fields beyond the body

| Field | How the skill sets it |
|---|---|
| Project | Resolved repo-first, then remembered, then asked (see SKILL.md). Referenced by URL-encoded full path. |
| Type | Asked each time: `Issue`, `Incident`, `Task`, or `Test case` → `issue_type`. `Test case` needs Ultimate tier. |
| Epic | Optional. Group-level, Premium/Ultimate only; sent via `epic_id`. Created without it (with a note) if rejected. |
| Title | A concise, verb/subject-led title derived from the brief; confirmed in preview. |
| Labels | Asked (optional). Sent via `labels` as a comma-separated string. **Only existing labels** — never invent one (GitLab silently creates unknown labels) and never add one the user didn't ask for; validate before applying. |
| Assignee | Optional. Resolve username → numeric id (`users?username=…`); sent via `assignee_ids[]`. Default unassigned. |
| Confidential | **Default on.** Always sent as `confidential=true` unless the user explicitly asks for a public work item. |
| Milestone | **Not set** by default (backlog). Optional; resolve title → numeric `milestone_id`. |
| Weight / Due date / Priority | **Not set** by the skill — left at project defaults. |

## Full skeleton (Issue / Task / Test case)

```
# Context

<context>

# Expected Result

<expected result>

# Acceptance Criteria

1. <one full-sentence criterion>
2. <one full-sentence criterion>

# Action Points               ← only if something must be confirmed before the work is buildable; stays VISIBLE
1. <what to confirm, with whom, and what changes depending on the answer>

# QA Notes                    ← only if there are API endpoints / console commands; keep this heading…
<endpoints (method + path) and/or command names with params, inside a collapsed <details> beneath the heading>

# Implementation Plan        ← only if a plan exists; keep this heading…
<plan body inside a collapsed <details> directly beneath the heading>
```

## Full skeleton (Incident)

```
# Context

<context>

## Data

* Account / Customer ID: …
* Affected entity ID: …

## Steps to Reproduce

1. …
2. …

## Actual Result

<what happens>

# Expected Result

<what should happen>

# Acceptance Criteria

1. <one full-sentence criterion>
2. <one full-sentence criterion>

# Action Points               ← only if something must be confirmed before the work is buildable; stays VISIBLE
1. <what to confirm, with whom, and what changes depending on the answer>

# QA Notes                    ← only if there are API endpoints / console commands; keep this heading…
<endpoints (method + path) and/or command names with params, inside a collapsed <details> beneath the heading>

# Implementation Plan        ← only if a plan exists; keep this heading…
<plan body inside a collapsed <details> directly beneath the heading>
```
