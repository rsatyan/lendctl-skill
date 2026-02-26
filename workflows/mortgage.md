# Mortgage Origination Workflow

Complete workflow for processing a mortgage application.

## Input Required
- Borrower: name, income details, credit score
- Property: price, down payment, taxes, insurance
- Loan: amount, term, type (conventional/FHA/VA)

## Workflow Steps

### Step 1: Income Analysis
```bash
# W-2 Employment
finctl income w2 --base $BASE --overtime $OT --bonus $BONUS --format json

# Self-Employed (if applicable)
finctl income self-emp --year1 $Y1 --year2 $Y2 --depreciation $DEP --format json
```

### Step 2: DTI Calculation
```bash
finctl dti --income $MONTHLY_INCOME --housing $PITI --debts $OTHER --format json
```
- Front-end DTI (housing only) should be ≤28%
- Back-end DTI (all debt) should be ≤43% for QM

### Step 3: Credit Analysis
```bash
creditctl score $FICO --format json
```
- 760+: Best rates
- 700-759: Good rates
- 620-699: FHA territory
- <620: Subprime, may need alternative

### Step 4: LTV Calculation
```bash
mortctl ltv --purchase-price $PRICE --down-payment $DOWN --format json
```
- ≤80%: No PMI required
- >80%: PMI required
- >95%: Limited programs available

### Step 5: Program Eligibility
```bash
mortctl eligible --loan-amount $AMT --credit-score $FICO --dti $DTI --ltv $LTV --format json
```

### Step 6: Payment Breakdown
```bash
mortctl payment --amount $AMT --rate $RATE --term $TERM --taxes $TAX --insurance $INS --credit-score $FICO --format json
```

### Step 7: Compliance Check
```bash
compctl atr --dti $DTI --term $TERM --format json
```
- QM Safe Harbor: DTI ≤43%, standard terms
- QM Rebuttable: DTI 43-50%
- Non-QM: Riskier, needs documentation

### Step 8: Audit Trail
```bash
auditctl log --tool mortctl --command "eligible" --tool-version 0.1.0 \
  --loan-id $LOAN_ID --inputs '{"amount":$AMT}' --outputs '{"eligible":true}' \
  --format json
```

## Decision Matrix

| DTI | LTV | Score | Decision |
|-----|-----|-------|----------|
| ≤43 | ≤80 | ≥700 | APPROVE |
| ≤43 | ≤95 | ≥620 | APPROVE w/PMI |
| ≤50 | ≤80 | ≥720 | APPROVE (compensating) |
| >50 | any | any | REFER to UW |
| any | >97 | any | DECLINE |

## Sample Output
```json
{
  "decision": "APPROVED",
  "product": "Conventional 30-Year Fixed",
  "amount": 520000,
  "rate": 6.5,
  "payment": 3979,
  "ltv": 80,
  "dti": 37.3,
  "qm_status": "safe_harbor",
  "conditions": ["VOE", "Appraisal", "Title"]
}
```
