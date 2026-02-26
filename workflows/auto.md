# Auto Loan Workflow

Complete workflow for processing an auto loan application.

## Input Required
- Borrower: income, credit score, employment
- Vehicle: year, make, model, price, value
- Loan: amount, down payment, term

## Workflow Steps

### Step 1: Income Verification
```bash
finctl income w2 --base $SALARY --format json
```

### Step 2: Credit Analysis
```bash
creditctl score $FICO --format json
```

Credit tiers for auto:
- 720+: Superprime (best rates)
- 680-719: Prime
- 620-679: Near-prime
- <620: Subprime

### Step 3: LTV Calculation
```bash
autoloanctl ltv --vehicle-value $VALUE --loan-amount $AMOUNT --format json
```

LTV Guidelines:
- New vehicle: Up to 125% LTV
- Used (1-3 years): Up to 110% LTV
- Used (4+ years): Up to 100% LTV
- High LTV → Recommend GAP insurance

### Step 4: Payment Calculation
```bash
autoloanctl payment --amount $AMOUNT --rate $RATE --term $TERM --credit-score $FICO --format json
```

Term recommendations by vehicle age:
- New: 36-72 months
- 1-3 years old: 36-60 months
- 4-6 years old: 24-48 months
- 7+ years old: 24-36 months max

### Step 5: Audit
```bash
auditctl log --tool autoloanctl --command "payment" --tool-version 0.1.0 \
  --loan-id $LOAN_ID --inputs '{"amount":$AMT}' --outputs '{"payment":$PMT}' \
  --format json
```

## Rate Tiers (Typical 2026)

| Tier | Score | New Car | Used Car |
|------|-------|---------|----------|
| Superprime | 720+ | 5.5% | 6.5% |
| Prime | 680-719 | 7.0% | 8.0% |
| Near-prime | 620-679 | 9.5% | 11.0% |
| Subprime | <620 | 14.0% | 16.0% |

## Decision Logic

```
IF credit_score >= 620 AND ltv <= max_ltv AND pti <= 20%:
    APPROVE
ELIF credit_score >= 580 AND ltv <= 100%:
    APPROVE with conditions (larger down payment)
ELSE:
    DECLINE with adverse action
```

## Sample Output
```json
{
  "decision": "APPROVED",
  "vehicle": "2024 Honda CR-V EX-L",
  "amount": 32000,
  "term": 60,
  "rate": 6.5,
  "payment": 626.12,
  "ltv": 88.9,
  "gap_recommended": true
}
```
