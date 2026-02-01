# BreakGlass Guardian Agent — Test Prompts (Validation)

Use these prompts to validate correctness, determinism, and cost efficiency.

---

## 1) Baseline (Provided break-glass UPNs)
**Goal:** Verify compliant/non-compliant logic with explicit break-glass list.

Prompt:
- Run BreakGlass Guardian with:
  - break_glass_upns: ["breakglass1@contoso.com","breakglass2@contoso.com"]
  - min_exclusions_required: 2
  - ignore_disabled_policies: true
  - sign_in_lookback_days: 7
Return the executive summary, both tables, and the final JSON block.

Expected:
- evaluation_mode = provided
- policies classified deterministically
- sign-in validation (if enabled) ONLY for the two break-glass UPNs

---

## 2) Non-Compliant scenario
**Goal:** Ensure Non-Compliant triggers when < min break-glass are excluded.

Prompt:
- Using break_glass_upns: ["breakglass1@contoso.com","breakglass2@contoso.com"]
Identify any enabled policy where fewer than 2 break-glass accounts are excluded.
Return only the Non-Compliant policies and the remediation JSON PATCH examples.

Expected:
- status = Non-Compliant for those policies
- reason references min_exclusions_required
- remediation prepared but not applied

---

## 3) At-Risk when no break-glass can be identified
**Goal:** Ensure At-Risk is used when reference list is empty.

Prompt:
- Run BreakGlass Guardian WITHOUT providing break_glass_upns.
Do not assume break-glass accounts exist unless discovered via pattern.
Return the executive summary and list all policies marked At-Risk with reasons.

Expected:
- evaluation_mode = none or discovered (empty)
- sign-in validation skipped (if enabled)
- At-Risk reasons clearly explain missing/unknown break-glass reference

---

## 4) Discovery mode (pattern-based)
**Goal:** Validate discovery and freezing of final reference list.

Prompt:
- Run BreakGlass Guardian WITHOUT break_glass_upns.
Use pattern discovery to find break-glass candidates.
Show the discovered break-glass UPNs and evaluate compliance with min_exclusions_required=2.

Expected:
- evaluation_mode = discovered
- discovered UPNs listed and used consistently
- no unrelated UPNs included in break-glass list

---

## 5) Disabled policies behavior
**Goal:** Confirm ignore_disabled_policies logic.

Prompt:
- Run BreakGlass Guardian twice:
  1) ignore_disabled_policies: true
  2) ignore_disabled_policies: false
Compare counts of evaluated policies and classification differences.

Expected:
- run #1 excludes disabled policies
- run #2 includes disabled policies
- consistent reasoning and counts

---

## 6) Sign-in validation scope (if enabled)
**Goal:** Ensure sign-in checks are performed only for break-glass UPNs.

Prompt:
- Run BreakGlass Guardian with break_glass_upns provided.
Verify that sign-in validation is performed ONLY for those break-glass UPNs
and not for any other effective excluded users.

Expected:
- activity table includes only break-glass UPNs
- no “not found in tenant” inference from sign-in results

---

## 7) SCU comparison (cost validation)
**Goal:** Validate SCU reduction behaviors.

Prompt:
- Run BreakGlass Guardian in a scenario where no break-glass can be identified
(no input UPNs, discovery returns none).
Confirm sign-in validation is skipped and report the SCU consumption for the run.

Expected:
- lower SCU vs baseline run with sign-in validation

---

## 8) Determinism (repeatability)
**Goal:** Ensure stable results across repeated runs.

Prompt:
- Run the same configuration 3 times in a row (same inputs).
Compare the final JSON blocks and confirm classification and counts are identical.

Expected:
- identical counts and statuses
- consistent evaluation_mode and reasoning
