# Credit Improvement Advisor Workflow

Help borrowers improve their credit score for better loan terms.

## Use Cases
1. Pre-application credit optimization
2. Rapid rescore simulation
3. Credit building strategy
4. Dispute recommendations

---

## Step 1: Current Credit Assessment

```bash
creditctl score $CURRENT_FICO --format json
```

Output includes:
- Current tier (superprime/prime/nearprime/subprime)
- Mortgage eligibility by program
- Auto loan rate tier
- Credit card tier

---

## Step 2: Identify Improvement Opportunities

### A. Utilization Analysis
```bash
creditctl analyze --format json
# Pass tradeline data to analyze utilization
```

**Target utilization**: <30% overall, <10% for maximum score impact

**Quick wins**:
| Current Util | Action | Expected Impact |
|-------------|--------|-----------------|
| >50% | Pay to 30% | +20-40 points |
| 30-50% | Pay to 10% | +10-20 points |
| 10-30% | Pay to <10% | +5-10 points |

### B. Payment History
- 35% of FICO score
- Any late payments in last 2 years?
- Collections or charge-offs?

### C. Credit Mix
- Revolving (cards) + Installment (loans) is ideal
- Too many cards? Too few?

### D. Recent Inquiries
- Hard pulls in last 12 months?
- Rate shopping (multiple mortgage/auto inquiries) groups together

---

## Step 3: Rapid Rescore Simulation

```bash
creditctl rescore --current-score $FICO --action "pay-down-cc" --amount $PAYDOWN --format json
```

### Common Rescore Actions

| Action | Typical Impact | Timeline |
|--------|----------------|----------|
| Pay card to 30% util | +15-30 pts | 30 days |
| Pay card to <10% util | +20-40 pts | 30 days |
| Remove collection | +25-50 pts | 30-45 days |
| Authorized user | +10-30 pts | 30 days |
| Pay off installment | -5 to +10 pts | 30 days |

---

## Step 4: Score Targets by Product

### Mortgage
| Score | Impact |
|-------|--------|
| 580 | FHA minimum |
| 620 | Conventional minimum |
| 680 | No LLPA adjustments |
| 740 | Best rates |
| 760+ | Maximum savings |

### Auto
| Score | Tier | Rate Impact |
|-------|------|-------------|
| 720+ | Superprime | Best rate |
| 680-719 | Prime | +1-2% |
| 620-679 | Near-prime | +3-5% |
| <620 | Subprime | +8-12% |

### Personal Loan
| Score | Rate Range |
|-------|------------|
| 750+ | 8-11% |
| 700-749 | 12-15% |
| 650-699 | 16-20% |
| <650 | 21-30% |

---

## Step 5: Calculate Savings Potential

Show borrower the dollar impact of improving their score:

### Example: Mortgage Savings
```
Current score: 680 → Rate: 7.0%
Target score: 740 → Rate: 6.5%
Loan amount: $400,000
Term: 30 years

Current payment: $2,661
Target payment: $2,528
Monthly savings: $133
Lifetime savings: $47,880
```

### Example: Auto Savings
```
Current score: 650 → Rate: 10%
Target score: 720 → Rate: 6%
Loan amount: $30,000
Term: 60 months

Current payment: $637
Target payment: $580
Monthly savings: $57
Total savings: $3,420
```

---

## Step 6: Action Plan

### Quick Wins (30 days)
1. Pay credit cards below 30% utilization
2. Request credit limit increases (soft pull)
3. Become authorized user on family member's old card
4. Dispute any errors on credit report

### Medium Term (60-90 days)
1. Pay cards below 10% utilization
2. Negotiate pay-for-delete on collections
3. Don't close old accounts
4. Avoid new credit applications

### Long Term (6-12 months)
1. Build payment history
2. Add credit mix if lacking
3. Let inquiries age off (12 months)
4. Let negative marks age (7 years)

---

## Sample Output

```json
{
  "current_score": 680,
  "target_score": 740,
  "tier_change": "nearprime → prime",
  "estimated_timeline": "60-90 days",
  "actions": [
    {
      "action": "Pay Visa from $4,500 to $1,500",
      "cost": 3000,
      "impact": "+25-35 points",
      "priority": 1
    },
    {
      "action": "Request CLI on Amex (soft pull)",
      "cost": 0,
      "impact": "+5-10 points",
      "priority": 2
    },
    {
      "action": "Dispute collection from 2019",
      "cost": 0,
      "impact": "+20-30 points if removed",
      "priority": 3
    }
  ],
  "savings_potential": {
    "mortgage_monthly": 133,
    "mortgage_lifetime": 47880,
    "auto_total": 3420
  },
  "recommendation": "Focus on paying down Visa first. Biggest impact per dollar spent."
}
```
