# Credit Card Workflow

Complete workflow for credit card application and management.

## Use Cases
1. New card application
2. Credit line increase request
3. Balance transfer analysis
4. APR negotiation prep

---

## 1. New Card Application

### Input Required
- Income, credit score, existing credit limits, monthly debt

### Step 1: Credit Check
```bash
creditctl score $FICO --format json
```

### Step 2: Credit Limit Calculation
```bash
cardctl limit --income $ANNUAL_INCOME --credit-score $FICO \
  --existing-limits $CURRENT_LIMITS --monthly-debt $DEBT --format json
```

### Step 3: APR Assignment
```bash
cardctl apr --credit-score $FICO --prime-rate 8.5 --format json
```

### Step 4: Audit
```bash
auditctl log --tool cardctl --command "limit" --tool-version 0.1.0 \
  --format json
```

---

## 2. Credit Line Increase

### Input Required
- Current limit, account age, payment history, utilization, score

### Eligibility Check
```bash
cardctl cli --current-limit $LIMIT --account-age $MONTHS \
  --payment-history excellent --utilization $UTIL --credit-score $FICO \
  --format json
```

### Requirements
- Account age ≥6 months
- Payment history: good or better
- No CLI in last 6 months
- Utilization matters (high util = needs increase)

---

## 3. Balance Transfer Analysis

### Input Required
- Balance to transfer, current APR, promo terms

### Analysis
```bash
cardctl transfer --balance $BAL --current-apr $APR \
  --promo-apr 0 --promo-months 18 --fee 3 --format json
```

### Decision Factors
- Total savings vs transfer fee
- Break-even month
- Can you pay off in promo period?

### Recommendation Logic
```
IF total_savings > 0 AND break_even < promo_months/2:
    "Strongly recommend transfer"
ELIF total_savings > 0:
    "Transfer recommended"
ELIF total_savings > -100:
    "Marginal benefit"
ELSE:
    "Not recommended"
```

---

## Credit Tiers

| Tier | Score | Limit Multiplier | Purchase APR |
|------|-------|------------------|--------------|
| Superprime | 750+ | 35% of income | 10.99% |
| Prime | 700-749 | 25% of income | 15.99% |
| Near-prime | 650-699 | 18% of income | 21.99% |
| Subprime | 580-649 | 12% of income | 25.99% |

## Policy Limits
- Min credit limit: $500
- Max credit limit: $50,000
- Max total credit: 75% of annual income
- Min income: $15,000

## Sample Outputs

### New Card Approval
```json
{
  "decision": "APPROVED",
  "limit": 12000,
  "purchase_apr": 15.99,
  "cash_advance_apr": 20.99,
  "balance_transfer_apr": 17.99,
  "annual_fee": 0
}
```

### CLI Approval
```json
{
  "eligible": true,
  "current_limit": 5000,
  "recommended_increase": 2500,
  "new_limit": 7500,
  "factors": ["Excellent payment history", "Account tenure 2+ years"]
}
```

### Balance Transfer
```json
{
  "current_balance": 5000,
  "transfer_fee": 150,
  "total_savings": 1489.27,
  "break_even_month": 2,
  "recommendation": "Transfer recommended. Save $1,489 over 18 months."
}
```
