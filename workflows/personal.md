# Personal Loan Workflow

Complete workflow for processing a personal/unsecured loan application.

## Input Required
- Borrower: income, credit score, employment months, monthly debt
- Loan: amount, purpose, preferred term

## Workflow Steps

### Step 1: Income Verification
```bash
finctl income w2 --base $SALARY --bonus $BONUS --format json
```

### Step 2: Credit Analysis
```bash
creditctl score $FICO --format json
```

Minimum requirements:
- Score ≥580 for unsecured
- Score ≥500 for secured (with collateral)

### Step 3: Eligibility Check
```bash
persctl eligible --amount $AMOUNT --income $ANNUAL_INCOME --credit-score $FICO \
  --monthly-debt $DEBT --format json
```

### Step 4: Rate Quote
```bash
persctl payment --amount $AMOUNT --credit-score $FICO --term $TERM --format json
```

### Step 5: Term Comparison
```bash
persctl payment --amount $AMOUNT --credit-score $FICO --compare --format json
```

Shows 24/36/48/60 month options side-by-side.

### Step 6: Compliance (if decline)
```bash
compctl adverse --reasons "dti,credit-score" --format json
```

### Step 7: Audit
```bash
auditctl log --tool persctl --command "eligible" --tool-version 0.1.0 \
  --loan-id $LOAN_ID --format json
```

## Rate Tiers (2026 Market)

| Tier | Score | Rate Range |
|------|-------|------------|
| Excellent | 750+ | 7.99-10.99% |
| Good | 700-749 | 11.99-14.99% |
| Fair | 650-699 | 15.99-19.99% |
| Poor | 600-649 | 22.99-25.99% |
| Subprime | <600 | 29.99%+ |

## Loan Purposes (Risk Adjusted)

| Purpose | Risk | Rate Adj |
|---------|------|----------|
| Debt consolidation | Low | +0% |
| Home improvement | Low | +0% |
| Major purchase | Medium | +0.5% |
| Medical | Medium | +0.5% |
| Vacation | High | +1.0% |
| Other | High | +1.5% |

## DTI Limits by Tier

| Tier | Max DTI |
|------|---------|
| Excellent | 50% |
| Good | 45% |
| Fair | 40% |
| Poor | 35% |

## Sample Output
```json
{
  "decision": "APPROVED",
  "amount": 25000,
  "rate": 12.5,
  "term": 48,
  "payment": 665.42,
  "origination_fee": 500,
  "total_cost": 31940.16,
  "purpose": "debt_consolidation"
}
```

## Debt Consolidation Analysis

If purpose is debt_consolidation, calculate savings:
```bash
# Show current debts vs consolidated payment
persctl payment --amount $TOTAL_DEBT --credit-score $FICO --compare --format json
```

Compare total interest paid on existing debts vs new loan.
