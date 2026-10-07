# Cross-check with the official skill-creator

The checklist reads a skill's text. Anthropic's skill-creator measures what the skill does: it runs the skill with and without itself on realistic prompts, grades the results, and tests whether the description triggers when it should. Running both and comparing them finds what neither finds alone, and the differences get settled from Anthropic's own pages, not from either tool's opinion.

## Contents
- Pick the tier
- Run the official pass blind
- Reconcile
- What the comparison means
- Learn from the gaps
- When it can't run
- Report it

## Pick the tier

The official pass is expensive: with-skill and without-skill runs for each test prompt, and three runs of every trigger query. Pick a tier per skill and say which in the report.

| Tier | When | The official pass runs |
|---|---|---|
| Full | The skill spends money, publishes, deploys, sends, orchestrates agents, or the user relies on it heavily | Behavior eval (3 prompts, with and without) and the trigger eval |
| Trigger only | Any other skill Claude starts on its own | The trigger eval |
| Off | A quick review, or a manual-only skill with nothing to run | Nothing; the report says the cross-check was not run |

Default to Full for the highest-risk skill in scope and Trigger only for the rest. The user can change the tier.

## Run the official pass blind

Start a fresh agent (a subagent with no history) that has not seen the checklist findings. Give it only the skill's path, the official skill-creator, and this brief:

```text
Evaluate the skill at [SKILL PATH] with the skill-creator skill. Do not edit the skill.
Write three realistic test prompts a real user would type, run each with the skill and without it, grade the outputs with the skill-creator's grader, and aggregate the benchmark.
[Full or Trigger only:] Write 16 to 20 trigger queries, half that should trigger and half near-misses that should not, and run the skill-creator's trigger eval (scripts/run_eval.py) with 3 runs per query.
Put every output in [WORKSPACE], outside the skill's folder.
Return: each test prompt with its with-skill and without-skill result, what the skill got wrong or did no better than no skill, the trigger rate for each query, and the queries it got wrong.
```

The official pass never sees the checklist results, and the checklist pass is finished and written down before the official results are read. Two passes that saw each other are one opinion.

## Reconcile

Put every finding from either pass in one table, then settle each difference:

1. **Both found it:** confirmed.
2. **Only one found it:** look it up in Anthropic's pages (the Sources in audit-checklist.md, or the official skill-creator's own guidance). Record the link and the date you checked it, then mark it **confirmed**, **dismissed** (say what the source says instead), or **unverified** (no source settles it). Without a source, it is never a verdict.
3. **They contradict each other** (for example, the checklist wants a more general description and the trigger eval shows it already fires on the right queries): the measured behavior wins for this skill, and the rule is noted as not mattering here.

## What the comparison means

| Checklist | Official pass | What it means |
|---|---|---|
| Rule fails | Behavior suffers | A real problem; it ranks first among the changes |
| Rule fails | Behavior is fine | Lower priority, or the rule doesn't matter for this skill. Say so; don't drop the finding silently |
| Rule passes | Behavior fails | A gap in the checklist. The most valuable thing a cross-check finds; see the next section |
| Description passes | Trigger eval misses queries | The description needs work; the trigger eval's misses are the evidence |
| No test prompts exist (TS1) | Prompts were written for the official pass | Offer them as a change: they can become the skill's evals |

Treat the official pass as evidence for TS1, TS2 and TS3: it is a fresh-session test with and without the skill, on the model that ran it.

## Learn from the gaps

When the official pass finds a problem that no checklist rule covers, write it as a candidate rule in `references/candidate-rules.md`: what failed, in which skill, on what date, and the rule that would have caught it. A candidate that shows up in audits of two different skills is ready to become a checklist rule; tell the user in the report. One audit makes it a candidate only, so a single odd skill can't reshape the checklist.

If the installed copy of this skill can't be edited, put the candidate lines in the report, and the user adds them to the repo.

When several disagreements cluster on one rule, re-read that rule's source page. Anthropic may have changed it. Update the rule and its checked date, or say in the report that the page changed.

## When it can't run

- **The official skill-creator isn't installed:** say so in the report and skip the cross-check. Never imitate it from memory.
- **The `claude` command-line tool isn't available** (the trigger eval needs it, so it usually runs in Claude Code only): run the behavior eval, skip the trigger eval, and say so.
- **Subagents aren't available:** run the official pass after the checklist pass is written, in the same session, and label the cross-check as not independent.
- **The budget runs out mid-pass:** report what finished. A pass that didn't finish is not evidence either way.

## Report it

The report's Cross-check section lists, in this order: the tier and what ran; what both passes found; what only the checklist found, with how each was settled; what only the official pass found, with how each was settled; the trigger results (the queries it got wrong); candidate rules added; and anything that couldn't run. Findings the cross-check confirms or changes keep their place in the decisions, changes and findings sections; the Cross-check section shows how they were settled.
