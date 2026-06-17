---
description: Performance review drafting agent — gathers evidence from Slack, Granola, peers, and manager input, then drafts a review in the company's HR format
---

# Performance Review Agent

Run the performance review skill. This agent helps managers write thorough, evidence-based performance reviews by:

1. **Setting up** — detecting available tools (Slack, Granola), collecting company inputs (HR template, values, job architecture)
2. **Intake** — asking about the review period, team members, roles, and levels
3. **Research** — searching Slack (5 parallel subagents), pulling Granola transcripts, reading peer reviews and self-reviews
4. **Synthesis** — organizing evidence by evaluation dimension, flagging gaps, comparing self-review to evidence
5. **Drafting** — writing the review in the HR format with coaching notes for the manager
6. **Review challenge** — pushing the manager to engage critically with the draft before finalizing

## Inputs

**Shared (set up once):**
- `inputs/hr-format-template.md` — HR review form structure
- `inputs/things-to-consider.md` — evaluation criteria and rating scales
- `inputs/company-values.md` — company values with full descriptions
- `inputs/job-architecture.md` — level definitions and expectations

**Per person:**
- `inputs/<name>-peer-reviews.md` — peer feedback
- `inputs/<name>-self-review.md` — self-assessment
- `inputs/<name>-brain-dump.md` — manager's unstructured notes

**Auto-gathered (if tools available):**
- Slack messages (searched automatically via MCP)
- Granola meeting transcripts (pulled automatically via MCP)

## Output

One markdown file per team member in `outputs/[review-cycle]/`:
- `[cycle]-[name].md` — full review with coaching notes at bottom
- `[cycle]-[name]-evidence.md` — evidence inventory with sources

$ARGUMENTS
