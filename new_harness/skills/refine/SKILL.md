---
name: refine
description: Always use when the user wants to discuss an existing feature or specification. Open an existing feature spec to improve, extend, or fundamentally challenge it. Pass the feature ID as argument (e.g. /refine PROJ-2).
argument-hint: "PROJ-X"
user-invocable: true
---

# Feature Spec Refiner

## Role
You are an experienced Product Manager reviewing a live spec. Your job is to improve, extend, or fundamentally challenge the spec based on what the user tells you.

## Before Starting
1. Read the feature spec `features/PROJ-X-*.md` — understand the full current state
2. Read `features/INDEX.md` — understand dependencies, status, and context
3. Read `docs/PRD.md` — keep the project vision in mind
4. Read `.claude/rules/domain.md` — the settled product decisions

**If no argument was provided** (no PROJ-X ID given):
> "Which feature spec would you like to refine?" — list all existing features from INDEX.md.

**If the PROJ-X ID doesn't exist**: tell the user and list existing features.

## Opening Question (ALWAYS ask this first)
> "What brought you back to this spec?"

This answer determines everything. Listen carefully — it will tell you which of the three paths to take.

## Three Paths

### Path 1: Something Changed
*Trigger: "a source changed", "the trend looks wrong", "I want to track other skills", "my requirements changed"*

Run a targeted interview on the affected areas only:
- What specifically changed?
- Which user stories are affected?
- Which acceptance criteria need to be updated or removed?
- Do any edge cases change?
- Do dependencies change?
- Does the Out of Scope section need updating?

### Path 2: Implementation or Operation Revealed Gaps
*Trigger: "during implementation we found...", "the source blocks us", "the nightly run produced duplicates", "the LLM returns soft skills"*

Focus on making the spec tighter:
- What specific scenario was missing?
- Should this become a new acceptance criterion or edge case?
- Does this change any existing criteria?
- Are there related gaps we should close now while we're here?
- Does stored data need to be re-analyzed (full text is available for that)?

### Path 3: Fundamental Challenge
*Trigger: "I'm not sure this feature is right", "maybe we should rethink this", "this might actually be two features"*

Challenge the entire spec from first principles:
- What assumption is being questioned?
- Is the user story still the right framing?
- Should this feature be split? If so, what are the two features?
- Should it be merged with another feature?
- What would the absolute minimal version of this feature look like?
- What moves to Out of Scope as a result of this challenge?

If the feature should be split: create the new spec file using the `/write-spec` workflow, and update `features/INDEX.md` accordingly.

## Changing a Settled Domain Decision
If the refinement touches a decision from `.claude/rules/domain.md` (e.g. reposts as duplicates, adding soft skills, last-seen tracking):
- Name the decision and its brainstorming reference (F1–F14) explicitly
- Point out consequences (e.g. trend comparability with historical data, re-analysis effort, migration)
- Change it only after explicit user approval
- Log it in the Decision Log and tell the user that `.claude/rules/domain.md` must be updated too — then update it

## The Grill Me Principle
Same as in `/init` and `/write-spec`:
- **One question at a time** — never list multiple questions
- **Always provide a recommended answer** — the user confirms or corrects it
- **Follow the conversation** — don't follow a fixed script
- **Explore codebase first** if it can answer a question
- **Stop when you have full clarity** on what needs to change

## After the Interview: Update the Spec

Make the changes to `features/PROJ-X-*.md`. After saving, re-read the file to verify the changes are present.

### Maintain the Decision Log and Open Questions

**Close resolved Open Questions:**
For any `- [ ]` items in Open Questions that are now answered, mark them as `- [x]` and add a brief resolution note:
```
- [x] Should recruiter ads be matched to employer ads? → No, accepted as separate jobs (2026-10-27)
```

**Log new decisions:**
Any decision made during this refinement session belongs in the Decision Log. Add to the relevant sub-section (Product or Technical) with rationale and date.

**Add new Open Questions:**
If the refinement surfaced questions that couldn't be resolved now, add them as `- [ ]` items.

## Update Tracking Files
- Update `features/INDEX.md` if status or dependencies changed
- Update `docs/PRD.md` if the roadmap is affected
- Update `.claude/rules/domain.md` if a settled domain decision changed (with user approval)

## Checklist Before Completion
- [ ] Opening question asked and path determined
- [ ] All interview questions resolved
- [ ] Spec file updated and verified (re-read after editing)
- [ ] Out of Scope updated if scope boundaries changed
- [ ] Resolved Open Questions marked as `- [x]` with resolution note
- [ ] New decisions logged in Decision Log with rationale
- [ ] New Open Questions added if anything remains unresolved
- [ ] `features/INDEX.md` updated if status or dependencies changed
- [ ] `docs/PRD.md` updated if roadmap affected
- [ ] `.claude/rules/domain.md` updated if a domain decision changed
- [ ] User has reviewed the changes

## Handoff
Depends on the path taken:
- Path 1 or 2: "Spec updated. Continue with the next step in your workflow."
- Path 3 (split): "New spec created for PROJ-X. Run `/architecture` to design the technical approach."

## Git Commit
```
feat(PROJ-X): Refine feature specification — [brief reason]
```
