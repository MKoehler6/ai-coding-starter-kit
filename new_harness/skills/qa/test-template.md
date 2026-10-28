# QA Test Results Template

Add this section to the END of the feature spec `features/PROJ-X-*.md`:

```markdown
---

## QA Test Results

**Tested:** YYYY-MM-DD
**Feature Type:** Pipeline | Dashboard | Infrastructure
**Environment:** local (API localhost:8000, dashboard localhost:5173, test DB) | server
**Tester:** QA Engineer (AI)

### Automated Tests
- Backend (pytest): X passed, Y failed
- Frontend (Vitest): X passed, Y failed
- E2E (Playwright): X passed, Y failed

### Acceptance Criteria Status

#### AC-1: [Criterion]
- [x] Passed
- [ ] BUG: Failed (describe what went wrong)

#### AC-2: [Criterion]
- [x] Passed

### Domain Rules
- [x] Cross-portal duplicate discarded, first found wins
- [x] Multi-location ad = one job
- [x] Repost = new job
- [x] Only hard/domain skills stored
- [x] Remote jobs included regardless of region filter
- [ ] BUG: [Violation description]

### Edge Cases Status

#### EC-1: [Edge Case Name]
- [x] Handled correctly

#### EC-2: [Edge Case Name]
- [ ] BUG: Not handled (describe expected vs actual behavior)

### Pipeline Robustness (if applicable)
- [x] Blocked source logged and skipped, run continues
- [x] Structural change detected (`parser_broken`)
- [x] LLM invalid output retried, then skipped
- [x] Geocoder failure → location kept without coordinates
- [x] Run is idempotent

### Security Audit Results
- [x] Stored XSS: scraped content rendered as text
- [x] Malicious links (`javascript:`) rejected
- [x] SQL injection via filter params blocked
- [x] Prompt injection in ad text has no effect on output schema
- [x] Input limits enforced (422 on invalid params)
- [x] No secrets in frontend bundle, logs, or git
- [x] Server exposure: API on 127.0.0.1, DB not public, dashboard protected (deployment features)
- [ ] BUG: [Security issue description]

### Bugs Found

#### BUG-1: [Bug Title]
- **Severity:** Critical | High | Medium | Low
- **Steps to Reproduce:**
  1. [Fixture / page / command]
  2. Do [action]
  3. Expected: [what should happen]
  4. Actual: [what actually happens]
- **Screenshot / Log excerpt:** [if applicable]
- **Priority:** Fix before deployment | Fix in next iteration | Nice to have

### Summary
- **Acceptance Criteria:** X/Y passed
- **Bugs Found:** N total (C critical, H high, M medium, L low)
- **Security:** [Pass / Issues found]
- **Production Ready:** YES / NO
- **Recommendation:** [Deploy / Fix bugs first]
```
