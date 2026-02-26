# Originations Agent Skill

Automate retail lending originations using the LendCtl Suite CLI tools.

## Workflows Available

- [Mortgage Origination](workflows/mortgage.md) — Full mortgage application processing
- [Auto Loan](workflows/auto.md) — Vehicle financing workflow
- [Personal Loan](workflows/personal.md) — Unsecured loan eligibility and pricing
- [Credit Card](workflows/card.md) — Card applications, CLI, balance transfers
- [Multi-Product Comparison](workflows/compare.md) — Help borrowers choose the right product
- [Credit Improvement Advisor](workflows/credit-advisor.md) — Score optimization strategies

## Tool Manifest

See [MANIFEST.md](MANIFEST.md) for a lightweight reference (~100 tokens).

## Tools Available

All tools output JSON with `--format json`. Use this for programmatic processing.

| Tool | Purpose | Install |
|------|---------|---------|
| `finctl` | Income/DTI calculations | `npm i -g finctl` |
| `creditctl` | Credit report analysis | `npm i -g creditctl` |
| `mortctl` | Mortgage underwriting | `npm i -g mortctl` |
| `autoloanctl` | Auto loan calculations | `npm i -g autoloanctl` |
| `persctl` | Personal loans | `npm i -g persctl` |
| `cardctl` | Credit card decisioning | `npm i -g cardctl` |
| `compctl` | Compliance checks | `npm i -g compctl` |
| `auditctl` | Audit logging | `npm i -g auditctl` |

## Quick Reference

### Income (finctl)

```bash
# W-2 income
finctl income w2 --base 95000 --overtime 8000 --bonus 5000 --format json

# Self-employed
finctl income self-emp --year1 120000 --year2 95000 --depreciation 15000 --format json

# Other income
finctl income other --type social-security --amount 2400 --frequency monthly --format json

# DTI
finctl dti --income 9000 --housing 2800 --debts 555 --format json

# Full analysis
finctl analyze --base 95000 --housing 2800 --debts 555 --format json
```

### Credit (creditctl)

```bash
# Score analysis
creditctl score 742 --format json

# Full report analysis (pass tradelines as JSON)
creditctl analyze --scores 742,738,745 --format json

# Rapid rescore simulation
creditctl rescore --current-score 680 --action "pay-down-cc" --amount 2000 --format json
```

### Mortgage (mortctl)

```bash
# LTV calculation
mortctl ltv --purchase-price 650000 --down-payment 130000 --format json

# Program eligibility
mortctl eligible --loan-amount 520000 --credit-score 742 --dti 37 --ltv 80 --format json

# Payment breakdown (PITI)
mortctl payment --amount 520000 --rate 6.5 --term 360 --taxes 6500 --insurance 1800 --format json

# Conforming limits
mortctl limits --county "Los Angeles" --state CA --format json
```

### Auto (autoloanctl)

```bash
# LTV check
autoloanctl ltv --vehicle-value 28500 --loan-amount 25000 --format json

# Payment calculation
autoloanctl payment --amount 25000 --rate 7.5 --term 60 --credit-score 720 --format json
```

### Personal (persctl)

```bash
# Eligibility
persctl eligible --amount 15000 --income 75000 --credit-score 720 --format json

# Payment with comparison
persctl payment --amount 10000 --credit-score 720 --compare --format json
```

### Credit Card (cardctl)

```bash
# Credit limit
cardctl limit --income 75000 --credit-score 720 --existing-limits 15000 --format json

# APR assignment
cardctl apr --credit-score 720 --prime-rate 8.5 --format json

# Balance transfer analysis
cardctl transfer --balance 5000 --current-apr 22 --promo-apr 0 --promo-months 18 --format json

# CLI eligibility
cardctl cli --current-limit 5000 --account-age 12 --payment-history excellent --utilization 45 --credit-score 720 --format json
```

### Compliance (compctl)

```bash
# ATR/QM check
compctl atr --dti 43 --term 360 --points 1.5 --format json

# TRID timing
compctl trid --app-date 2026-02-01 --le-date 2026-02-03 --format json

# Adverse action notice
compctl adverse --reasons "dti,credit-score" --format json
```

### Audit (auditctl)

```bash
# Log an action
auditctl log --tool finctl --action "income-w2" --loan-id 12345 --inputs '{"base":95000}' --outputs '{"monthly":9000}' --format json

# Query trail
auditctl query --loan-id 12345 --format json

# Verify integrity
auditctl verify --loan-id 12345 --format json

# Export for exam
auditctl export --loan-id 12345 --format occ
```

## Origination Workflow

### Step 1: Detect Product Type
From the application, determine: mortgage, auto, personal, or card.

### Step 2: Income Analysis
```bash
# Always start with income
finctl income w2 --base $BASE --overtime $OT --bonus $BONUS --format json

# Calculate DTI
finctl dti --income $MONTHLY_INCOME --housing $PITI --debts $OTHER_DEBTS --format json
```

### Step 3: Credit Analysis
```bash
creditctl score $SCORE --format json
```

### Step 4: Product-Specific Underwriting

**Mortgage:**
```bash
mortctl ltv --purchase-price $PRICE --down-payment $DOWN --format json
mortctl eligible --loan-amount $AMOUNT --credit-score $SCORE --dti $DTI --ltv $LTV --format json
mortctl payment --amount $AMOUNT --rate $RATE --term $TERM --taxes $TAXES --insurance $INS --format json
```

**Auto:**
```bash
autoloanctl ltv --vehicle-value $VALUE --loan-amount $AMOUNT --format json
autoloanctl payment --amount $AMOUNT --rate $RATE --term $TERM --credit-score $SCORE --format json
```

**Personal:**
```bash
persctl eligible --amount $AMOUNT --income $INCOME --credit-score $SCORE --format json
persctl payment --amount $AMOUNT --credit-score $SCORE --term $TERM --format json
```

**Credit Card:**
```bash
cardctl limit --income $INCOME --credit-score $SCORE --existing-limits $EXISTING --format json
cardctl apr --credit-score $SCORE --format json
```

### Step 5: Compliance Check
```bash
# For mortgage
compctl atr --dti $DTI --term $TERM --points $POINTS --format json

# For all products on decline
compctl adverse --reasons "$REASONS" --format json
```

### Step 6: Log Everything
```bash
auditctl log --tool $TOOL --action $ACTION --loan-id $LOAN_ID --inputs '$INPUTS' --outputs '$OUTPUTS'
```

### Step 7: Decision

Based on all outputs:
- **APPROVE** if all checks pass
- **DECLINE** if hard fails (DTI > max, score < min)
- **REFER** if edge cases need human review

## Sample Application JSON

```json
{
  "product": "mortgage",
  "borrower": {
    "name": "John Smith",
    "income": {
      "type": "w2",
      "base": 95000,
      "overtime": 8000,
      "bonus": 5000
    },
    "credit_score": 742,
    "monthly_debts": 555
  },
  "loan": {
    "amount": 520000,
    "term": 360,
    "rate": 6.5
  },
  "property": {
    "price": 650000,
    "down_payment": 130000,
    "taxes": 6500,
    "insurance": 1800
  }
}
```

## Decision Output Format

```json
{
  "decision": "APPROVED",
  "product": "Conventional 30-Year Fixed",
  "loan": {
    "amount": 520000,
    "rate": 6.5,
    "term": 360,
    "payment": 3979
  },
  "ratios": {
    "ltv": 80,
    "dti_front": 31.1,
    "dti_back": 37.3
  },
  "compliance": {
    "qm_status": "safe_harbor",
    "atr_compliant": true
  },
  "conditions": [
    "VOE within 10 days of closing",
    "Appraisal required"
  ],
  "audit_id": "aud_abc123"
}
```

## Error Handling

If a tool returns an error or the application is incomplete:
1. Log the error to audit trail
2. Report what's missing
3. Do NOT make up data — ask for clarification

## Tips

- Always use `--format json` for parsing
- Log EVERY tool call with auditctl
- Check compliance BEFORE rendering decision
- Generate adverse action notice for ALL declines
