# LendCtl Skill

Production-ready AI agent skill for automated loan origination using the LendCtl CLI tools.

## LLM Compatibility

Works with **any LLM capable of tool/function calling**:

| Provider | Models | Integration |
|----------|--------|-------------|
| **OpenAI** | GPT-4, GPT-4o, GPT-4-turbo | Function calling or shell tool |
| **Anthropic** | Claude 3.5 Sonnet, Claude 3 Opus | Tool use or computer use |
| **Google** | Gemini Pro, Gemini Ultra | Function calling |
| **Meta** | Llama 3, Llama 3.1 | Via frameworks with tool support |
| **Mistral** | Mixtral, Mistral Large | Function calling |
| **Open-source** | Qwen, DeepSeek, Command-R | Via shell/exec tools |

**Requirements:**
- Model must be able to execute shell commands OR have function calling
- All tools output JSON with `--format json`
- No API keys needed — tools run locally

## Installation

```bash
# Install all LendCtl CLI tools
npm i -g finctl-cli creditctl mortctl autoloanctl persctl cardctl compctl auditctl
```

## Quick Start

### For Agents with Shell Access

```bash
# Check mortgage eligibility in 3 commands
finctl income w2 --base 95000 --overtime 8000 --format json
creditctl score 742 --format json
mortctl eligible --loan-amount 520000 --credit-score 742 --dti 37 --ltv 80 --format json
```

### For Function-Calling Agents

Define tools that wrap CLI commands:

```json
{
  "name": "calculate_income",
  "description": "Calculate qualifying income from W-2 employment",
  "parameters": {
    "base": { "type": "number", "description": "Annual base salary" },
    "overtime": { "type": "number", "description": "Annual overtime" },
    "bonus": { "type": "number", "description": "Annual bonus" }
  }
}
```

Implementation executes: `finctl income w2 --base {base} --overtime {overtime} --bonus {bonus} --format json`

## Workflows

### 🏠 Mortgage Origination
Full workflow from application to compliance check.
- Income verification (W-2, self-employed, other)
- Credit analysis and scoring
- LTV/PMI calculations
- Program eligibility (Conventional, FHA, VA)
- ATR/QM compliance
- Audit trail

[View full workflow →](workflows/mortgage.md)

### 🚗 Auto Loan
Vehicle financing with complete underwriting.
- Credit tier assignment
- Vehicle LTV analysis
- Payment calculations
- GAP insurance recommendations
- Term limits by vehicle age

[View full workflow →](workflows/auto.md)

### 💰 Personal Loan
Unsecured lending eligibility and pricing.
- Income and DTI verification
- Risk-based rate assignment
- Multi-term comparison
- Debt consolidation analysis

[View full workflow →](workflows/personal.md)

### 💳 Credit Card
Card applications, increases, and transfers.
- Credit limit calculations
- APR tier assignment
- Balance transfer break-even analysis
- CLI eligibility checks

[View full workflow →](workflows/card.md)

### ⚖️ Multi-Product Comparison
Help borrowers choose the right product.
- Personal loan vs HELOC vs cash-out refi
- Debt consolidation scenarios
- Total cost comparisons

[View full workflow →](workflows/compare.md)

### 📈 Credit Improvement Advisor
Score optimization strategies.
- Current score analysis
- Rapid rescore simulation
- Action plan with priorities
- Savings projections by score target

[View full workflow →](workflows/credit-advisor.md)

## Tool Reference

See [MANIFEST.md](MANIFEST.md) for a lightweight tool reference (~100 tokens).

### All Tools

| Tool | Commands | Description |
|------|----------|-------------|
| `finctl` | `income w2`, `income self-emp`, `income other`, `dti`, `analyze` | Income and DTI calculations |
| `creditctl` | `score`, `analyze`, `rescore`, `tradelines` | Credit report analysis |
| `mortctl` | `ltv`, `eligible`, `payment`, `limits` | Mortgage underwriting |
| `autoloanctl` | `ltv`, `payment` | Auto loan calculations |
| `persctl` | `eligible`, `payment` | Personal loan eligibility |
| `cardctl` | `limit`, `apr`, `transfer`, `cli` | Credit card decisioning |
| `compctl` | `trid`, `atr`, `adverse` | Compliance checks |
| `auditctl` | `log`, `query`, `verify`, `replay`, `export` | Audit trail |

### Common Patterns

```bash
# All tools support JSON output
<tool> <command> --format json

# All tools have help
<tool> --help
<tool> <command> --help
```

## Sample Applications

See [templates/](templates/) for sample JSON applications:
- `mortgage_application.json`
- `auto_application.json`
- `personal_application.json`
- `card_application.json`

## Integration Examples

### OpenClaw / Claude Desktop

Copy `SKILL.md` to your skills directory. The agent can then process loan applications using the CLI tools.

### LangChain

```python
from langchain.tools import ShellTool

shell = ShellTool()

# Agent can call:
result = shell.run("finctl income w2 --base 95000 --format json")
```

### AutoGPT / AgentGPT

Add the CLI tools to your allowed commands list. The agent will discover and use them via `--help`.

### Custom Integration

```typescript
import { execSync } from 'child_process';

function calculateIncome(base: number, overtime?: number, bonus?: number) {
  const cmd = `finctl income w2 --base ${base} ${overtime ? `--overtime ${overtime}` : ''} ${bonus ? `--bonus ${bonus}` : ''} --format json`;
  return JSON.parse(execSync(cmd).toString());
}
```

## License

Apache-2.0 © Satyan Avatara
