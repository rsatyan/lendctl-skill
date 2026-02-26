# LendCtl Skill

AI agent skill for automated loan origination using the LendCtl CLI tools.

## Overview

This skill enables AI agents (Claude, GPT, etc.) to underwrite retail loans using composable CLI tools. Based on research showing CLI tools are 35x more token-efficient than MCP for AI agents.

## Prerequisites

Install the LendCtl CLI tools:

```bash
npm i -g finctl-cli creditctl mortctl autoloanctl persctl cardctl compctl auditctl
```

## Workflows Included

| Workflow | Description |
|----------|-------------|
| [mortgage.md](workflows/mortgage.md) | Full mortgage origination |
| [auto.md](workflows/auto.md) | Auto loan processing |
| [personal.md](workflows/personal.md) | Personal loan eligibility |
| [card.md](workflows/card.md) | Credit card decisioning |
| [compare.md](workflows/compare.md) | Multi-product comparison |
| [credit-advisor.md](workflows/credit-advisor.md) | Credit improvement advisor |

## Quick Start

1. Install the CLI tools (see above)
2. Copy `SKILL.md` to your agent's skills directory
3. Agent can now process loan applications

## Example

```bash
# Check mortgage eligibility
finctl income w2 --base 95000 --format json
creditctl score 742 --format json
mortctl eligible --loan-amount 520000 --credit-score 742 --dti 37 --ltv 80 --format json
```

## Tool Manifest

See [MANIFEST.md](MANIFEST.md) for a lightweight tool reference (~100 tokens).

## Sample Applications

See [templates/](templates/) for sample application JSON files.

## Related Packages

| Package | npm | Purpose |
|---------|-----|---------|
| finctl-cli | [npm](https://npmjs.com/package/finctl-cli) | Income/DTI |
| creditctl | [npm](https://npmjs.com/package/creditctl) | Credit analysis |
| mortctl | [npm](https://npmjs.com/package/mortctl) | Mortgage UW |
| autoloanctl | [npm](https://npmjs.com/package/autoloanctl) | Auto loans |
| persctl | [npm](https://npmjs.com/package/persctl) | Personal loans |
| cardctl | [npm](https://npmjs.com/package/cardctl) | Credit cards |
| compctl | [npm](https://npmjs.com/package/compctl) | Compliance |
| auditctl | [npm](https://npmjs.com/package/auditctl) | Audit trails |

## License

Apache-2.0 © Avatar Consulting
