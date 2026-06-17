# Performance Review Agent

A Claude Code skill that helps managers write thorough, evidence-based performance reviews. It gathers data from multiple sources (Slack, Granola meeting notes, peer reviews, self-reviews, and manager notes), synthesizes the evidence, and drafts a complete review in your company's HR format, including private coaching notes for the review conversation.

## How It Works

```
 PHASE 0: FIRST-TIME SETUP
 ┌─────────────────────────────────────────────────────────┐
 │  Step 1: Meet the manager                                │
 │          "What's your name, role, company, team scope?"  │
 │                                                          │
 │  Step 2: Tool detection                                  │
 │          [x/!] Slack MCP    (auto-search or manual)      │
 │          [x/!] Granola MCP  (auto-pull or manual)        │
 │                                                          │
 │  Only runs once. Skipped on subsequent sessions.         │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 1: INTAKE INTERVIEW
 ┌─────────────────────────────────────────────────────────┐
 │  Step 1: Ask review period + team member names           │
 │  Step 2: For each person, get role + level               │
 │  Step 3: Per-person checklist:                           │
 │          [ ] Peer reviews uploaded?                       │
 │          [ ] Self-review uploaded?                        │
 │          [ ] Brain dump provided?                         │
 │          [ ] Meeting notes folder? (or manual upload)     │
 │  Only proceed when all items checked.                    │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 2: PARSE ALL INPUTS
 ┌─────────────────────────────────────────────────────────┐
 │  Read HR template, evaluation criteria, company values,  │
 │  job architecture, peer reviews, self-review, brain dump │
 │  Summarize what was absorbed before moving on.           │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 3: SOURCE HIERARCHY (internal)
 ┌─────────────────────────────────────────────────────────┐
 │  1. Manager brain dump    (primary signal)               │
 │  2. Peer reviews          (corroborating evidence)       │
 │  3. Slack evidence        (objective, timestamped)       │
 │  4. Meeting notes         (behavior, decisions)          │
 │  5. Self-review           (reference only)               │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 4: SLACK RESEARCH (5 parallel subagents)
 ┌─────────────────────────────────────────────────────────┐
 │  Subagent 1: Direct contributions (from: person)         │
 │  Subagent 2: Mentions + recognition (@person)            │
 │  Subagent 3: Project involvement (key projects)          │
 │  Subagent 4: Collaboration signals (threads, help)       │
 │  Subagent 5: DMs with manager (private context)          │
 │  All run in parallel. Save quotes + timestamps.          │
 │                                                          │
 │  Skipped if Slack MCP not available.                     │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 5: MEETING NOTES RESEARCH
 ┌─────────────────────────────────────────────────────────┐
 │  If Granola: pull FULL TRANSCRIPTS (not summaries)       │
 │  If manual: read uploaded notes                          │
 │  Extract: action items, decisions, ownership signals     │
 │  Note patterns in participation + contribution.          │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 6: EVIDENCE SYNTHESIS
 ┌─────────────────────────────────────────────────────────┐
 │  Organize evidence by HR template dimensions             │
 │  Flag thin areas, highlight standout moments             │
 │  Compare self-review vs. evidence (undersell/oversell)   │
 │  Present evidence inventory to manager.                  │
 │  Ask: anything missing, wrong, or to emphasize?          │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 7: DRAFT THE REVIEW
 ┌─────────────────────────────────────────────────────────┐
 │  Write in third person. No em dashes. No dividers.       │
 │  No job levels in review body.                           │
 │  Sections follow the HR template structure.              │
 │  Tone: empathetic + direct (Radical Candor).             │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 8: COACHING NOTES (manager's eyes only)
 ┌─────────────────────────────────────────────────────────┐
 │  Self-review vs. review gaps (undersell/oversell)        │
 │  Sensitive topics + how to frame them                    │
 │  Conversation starters for the 1:1                       │
 │  General advice for handling the conversation            │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 PHASE 9: MANAGER REVIEW CHALLENGE
 ┌─────────────────────────────────────────────────────────┐
 │  1. Share the draft. Ask manager to read it fully.       │
 │  2. Challenge: "What would you change? Be specific."     │
 │  3. If vague response, push with targeted questions:     │
 │     - Comfortable with ratings? Walk me through why.     │
 │     - Anything you wouldn't say out loud in the 1:1?     │
 │     - Any example that feels like a stretch?             │
 │     - Tone right, or too soft/harsh anywhere?            │
 │  4. Apply edits, adjust narrative to match.              │
 │  Only final when manager explicitly confirms.            │
 └─────────────────────────────────────────────────────────┘
                            |
                            v
 OUTPUT
 ┌─────────────────────────────────────────────────────────┐
 │  outputs/[cycle]/[cycle]-[name].md          (review)     │
 │  outputs/[cycle]/[cycle]-[name]-evidence.md (evidence)   │
 │  Repeat from Phase 1 for next team member.               │
 └─────────────────────────────────────────────────────────┘
```

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured

### Optional MCP Integrations

The agent works best with these MCP servers connected, but adapts gracefully without them:

- **Slack MCP** — enables automatic Slack research across all channels and DMs
- **Granola MCP** — enables automatic meeting transcript pulling. If you use Granola, organize your meetings into **one folder per team member** (e.g., "Alex 1:1") containing all relevant conversations (1:1s, standups, project meetings). The agent pulls all transcripts from the folder you point it to.

If these aren't connected, the agent will ask you to upload evidence manually.

### Installation

1. Clone this repo into your working directory:

```bash
git clone https://github.com/raphaelreap/perf-review-agent.git
cd perf-review-agent
```

2. Run the agent:

```bash
claude
# then type: /perf-review
```

3. The agent will walk you through setup on first run.

## Directory Structure

```
perf-review-agent/
  .claude/
    commands/
      perf-review.md          # Slash command definition
    skills/
      perf-review/
        SKILL.md              # Full agent skill definition
  inputs/
    hr-format-template.md     # HR review form structure
    things-to-consider.md     # Evaluation criteria + rating scales
    company-values.md         # Company values (full descriptions)
    job-architecture.md       # Level definitions + expectations
    [name]-peer-reviews.md    # Per-person: peer feedback (you provide)
    [name]-self-review.md     # Per-person: self-assessment (you provide)
    [name]-brain-dump.md      # Per-person: manager's notes (you provide)
  outputs/
    [cycle]/
      [cycle]-[name].md           # Final review + coaching notes
      [cycle]-[name]-evidence.md  # Evidence inventory with sources
```

## What You Need to Provide

**Once (first review cycle):**
- Your company's HR review template (replace `inputs/hr-format-template.md`)
- Your company's values (replace `inputs/company-values.md`)
- Your job architecture / leveling framework (replace `inputs/job-architecture.md`)
- Your evaluation criteria (replace `inputs/things-to-consider.md`)

**Per team member:**
- Peer reviews (paste or upload)
- Self-review (paste or upload)
- Your brain dump (unstructured notes, thoughts, impressions)

## Writing Style

The agent writes reviews that are:

- **Third person** ("She has shown," not "You have shown")
- **Empathetic + direct** (Radical Candor: care personally, challenge directly)
- **Specific** (every claim anchored to a concrete example)
- **Evidence-backed** (Situation, Behavior, Impact structure)
- **Tight** (400-600 words per section; no filler, no corporate speak)
- **Calibrated** (matched to actual performance level, no inflation)
- **Forward-looking** (growth areas framed as opportunities with action steps)

## Source Hierarchy

Not all evidence carries equal weight. The agent follows this priority:

1. **Manager brain dump** — your assessment is the primary signal
2. **Peer reviews** — corroborating evidence from daily collaborators
3. **Slack evidence** — objective, timestamped contributions
4. **Meeting notes** — how the person shows up, decisions they drive
5. **Self-review** — reference only, never drives the narrative

## License

MIT
