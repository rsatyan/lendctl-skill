# Multi-Product Comparison Workflow

Help borrowers choose between different loan products for the same need.

## Common Scenarios

### Scenario 1: Debt Consolidation
"Should I use a personal loan, balance transfer, or HELOC?"

### Scenario 2: Home Improvement
"Personal loan vs HELOC vs cash-out refinance?"

### Scenario 3: Large Purchase
"Credit card vs personal loan vs auto loan?"

---

## Debt Consolidation Comparison

### Input Required
- Total debt to consolidate
- Current APRs on existing debt
- Home equity (if any)
- Credit score, income

### Step 1: Gather Current State
```bash
# Calculate current cost of debt
# (Manual calculation from user-provided data)
```

### Step 2: Personal Loan Option
```bash
persctl eligible --amount $TOTAL_DEBT --income $INCOME --credit-score $FICO --format json
persctl payment --amount $TOTAL_DEBT --credit-score $FICO --compare --format json
```

### Step 3: Balance Transfer Option
```bash
cardctl transfer --balance $TOTAL_DEBT --current-apr $AVG_APR \
  --promo-apr 0 --promo-months 18 --fee 3 --format json
```

### Step 4: HELOC/Home Equity (if applicable)
```bash
# Future: helocctl
# For now, estimate: prime + 1-2%, longer terms available
```

### Comparison Matrix

| Factor | Personal Loan | Balance Transfer | HELOC |
|--------|--------------|------------------|-------|
| Rate | 8-25% fixed | 0% promo, then 20%+ | 7-10% variable |
| Term | 2-7 years | 12-21 months promo | 10-20 years |
| Fee | 1-5% origination | 3-5% transfer | Closing costs |
| Collateral | None | None | Home |
| Tax deductible | No | No | Maybe |

### Decision Logic
```
IF debt < $5000 AND can_pay_in_promo_period:
    RECOMMEND "Balance Transfer"
ELIF has_home_equity AND debt > $25000 AND good_credit:
    RECOMMEND "HELOC" 
ELSE:
    RECOMMEND "Personal Loan"
```

---

## Home Improvement Comparison

### Personal Loan
```bash
persctl eligible --amount $PROJECT_COST --income $INCOME --credit-score $FICO --format json
```
- Pros: No collateral, fast funding
- Cons: Higher rate, shorter term

### HELOC
- Pros: Lower rate, tax deductible, flexible draw
- Cons: Home at risk, closing costs, variable rate

### Cash-Out Refinance
```bash
mortctl payment --amount $NEW_LOAN --rate $RATE --term 360 --format json
```
- Pros: Fixed rate, longest term, lowest payment
- Cons: Highest closing costs, resets mortgage clock

### Recommendation Framework
| Project Size | Credit | Equity | Recommendation |
|-------------|--------|--------|----------------|
| <$15K | Any | Any | Personal Loan |
| $15-50K | Good | Yes | HELOC |
| $15-50K | Fair | No | Personal Loan |
| >$50K | Good | Yes | Cash-out Refi |

---

## Sample Comparison Output

```json
{
  "scenario": "debt_consolidation",
  "amount": 15000,
  "options": [
    {
      "product": "Personal Loan",
      "rate": 12.5,
      "term": 48,
      "payment": 399.20,
      "total_cost": 19161.60,
      "total_interest": 4161.60,
      "pros": ["Fixed rate", "Fixed payment", "No collateral"],
      "cons": ["Higher rate than secured"]
    },
    {
      "product": "Balance Transfer",
      "promo_rate": 0,
      "promo_months": 18,
      "transfer_fee": 450,
      "payment_to_payoff": 833.33,
      "total_cost": 15450,
      "total_interest": 0,
      "pros": ["0% interest", "Lowest cost if paid in promo"],
      "cons": ["High payment required", "Rate jumps after promo"]
    },
    {
      "product": "HELOC",
      "rate": 8.5,
      "term": 120,
      "payment": 186.29,
      "total_cost": 22354.80,
      "total_interest": 7354.80,
      "pros": ["Lowest payment", "Tax deductible"],
      "cons": ["Home at risk", "Variable rate", "Longest payoff"]
    }
  ],
  "recommendation": "Balance Transfer if you can pay $833/mo; otherwise Personal Loan"
}
```
