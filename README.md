# Claude Code Configuration Study — Brent Crude × Geopolitical Risk

A controlled comparison of Claude Code configurations on one real analytical task.
Five layers of configuration are added one at a time; at each layer the same
prompts are run and the output is scored against a fixed checklist.

**The question:** which configuration layers earn their setup cost, and which are
decoration?

The analysis itself — does Middle East conflict activity carry information about
Brent crude returns — is the workload, not the point. It was chosen because it has
enough ways to go quietly wrong that a checklist can tell a good run from a
plausible-looking one.

**Model Used:** Opus 4.8

## TL;DR

**_For a more detailed run-through of the experiments, refer to this [file](https://github.com/cmmtscrpr4/Claude_Experiments/blob/main/Claude_Code_Workflow.pdf). If you are our colleagues here only for the agent artifacts, head [here](https://github.com/cmmtscrpr4/Claude_Experiments/tree/layer-5-commands/.claude)_**

The findings from the experiments showed that the most efficient workflow was not to "use the most agentic configuration." 

The results showed a hierarchy: first, remove ambiguity from the task, next, persist stable project knowledge, and finally add delegation only when the task is long or complex enough to benefit from it.

| Comparison | What was found|
| --- | --- |
| Short vs Detailed Prompt | Write a proper task specification before adding more architecture. |
| Short vs Detailed Prompt with Brief + Subagents | A strong architecture cannot replace task details. |
| Adding Brief to Short prompt | Standing project context helps but only partially. |
| Adding Rules to Short prompt | Rules rmeove known failure modes and reduce repeated instructions. |
| Brief with Subagents vs Brief only | Delegation trades more total work for a cleaner main context window. |
| Inline subagents vs Commands | Use commands for standardisation/reusability, not because they are cheaper. |
| Plan vs Accept Edits | Use plan mode when a wrong assumption is expensive. |
 
## The branches

Each layer adds exactly one thing to the layer below it, so a diff between
adjacent branches is that layer's contribution and nothing else.

| Branch | Adds | What it isolates |
| --- | --- | --- |
| `layer-1-base` | nothing — base project | the floor everything is measured against |
| `layer-2-rules` | `.claude/rules/` | the same rule text as a file rather than typed into the prompt |
| `layer-3-claude-md` | `.claude/CLAUDE.md` | a persistent project brief loaded on every turn |
| `layer-4-subagents` | `.claude/agents/` | delegation, each agent with its own context window |
| `layer-5-commands` | `.claude/commands/` | a fixed invocation instead of a remembered sequence |

Nineteen test cases hang off these five branches as their own `case/*` branches —
one configuration and one output each.

Overlays are cumulative: layer 5 contains everything layers 2–4 added.

## The 19 cases

One Claude Code session each. Nothing is reused between sessions: a compacted or
carried-over context is a treatment nobody intended to apply.

| Case | Branch | Stage | Prompt | What it isolates |
| --- | --- | --- | --- | --- |
| L1-EDA-01 | `layer-1-base` | EDA | short | the floor |
| L1-EDA-02 | `layer-1-base` | EDA | short + rules pasted in | rules delivered in the message |
| L1-EDA-03 | `layer-1-base` | EDA | detailed | the number every layer must beat |
| L1-EDA-04 | `layer-1-base` | EDA | detailed + rules pasted in | do rules add anything to a detailed prompt |
| L2-EDA-01 | `layer-2-rules` | EDA | short | vs L1-EDA-02: same text, file instead of message |
| L2-EDA-02 | `layer-2-rules` | EDA | detailed | what rules say that a good prompt does not |
| L2-FC-01 | `layer-2-rules` | forecast | short | forecasting has more ways to go wrong |
| L2-FC-02 | `layer-2-rules` | forecast | detailed | which items survive prompt + rules |
| L3-EDA-01 | `layer-3-claude-md` | EDA | short | what a persistent brief adds |
| L3-EDA-02 | `layer-3-claude-md` | EDA | short, **rules disabled** | the brief's contribution on its own |
| L3-EDA-03 | `layer-3-claude-md` | EDA | detailed | the ceiling without delegation |
| L3-FC-01 | `layer-3-claude-md` | forecast | short | context does not enforce sequencing |
| L3-FC-02 | `layer-3-claude-md` | forecast | detailed | whatever fails here is the case for layer 4 |
| L4-EDA-01 | `layer-4-subagents` | EDA | short | can delegation rescue a short prompt |
| L4-EDA-02 | `layer-4-subagents` | EDA | detailed | peak context vs L3-EDA-03 |
| L4-FC-01 | `layer-4-subagents` | forecast | short | does the auditor fire unprompted |
| L4-FC-02 | `layer-4-subagents` | forecast | detailed | was the audit proactive or requested |
| L5-EDA-01 | `layer-5-commands` | EDA | `/run-analysis` | same quality for one line of typing? |
| L5-FC-01 | `layer-5-commands` | forecast | `/run-forecast` | the audit becomes unskippable |

Two deliberate asymmetries:

- **No forecasting at layer 1.** Stage 2 should not start without a Stage 1
  report, and nothing at layer 1 enforces that ordering. A run there would
  measure whether the model happened to profile first, which is a coin flip, not
  a treatment.
- **L3-EDA-02 removes something** rather than adding it. Run with
  `build_branch.sh layer-3-claude-md --no-rules`, it separates the brief's
  contribution from the rules'.

### What gets recorded per case

Quality comes from the checklist:

- setup effort (1–10, subjective) 
- test cases passed
- token-limit share 
- time taken 
- context window share

### The one thing nothing fixed

The ACLED publication-lag check failed in **every column of the study**, including
subagents and commands. It is written down in the rules, path-scoped to the files
being edited, and repeated in the `data-prep` agent. It was still missed every
time.

Whatever configuration set, it does not buy attention to a requirement with no
structural consequence in the code — nothing breaks, no test fails, and the output
looks correct.

---