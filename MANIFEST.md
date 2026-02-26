# LendCtl Tools Manifest

> Lightweight reference for AI agents (~100 tokens vs 50,000+ for MCP schemas)

## Quick Install
```bash
npm i -g finctl-cli creditctl mortctl autoloanctl persctl cardctl compctl auditctl
```

## Tools

| Tool | Purpose | Key Commands |
|------|---------|--------------|
| `finctl` | Income/DTI | `income w2`, `income self-emp`, `dti`, `analyze` |
| `creditctl` | Credit analysis | `score`, `analyze`, `rescore`, `tradelines` |
| `mortctl` | Mortgage UW | `ltv`, `eligible`, `payment`, `limits` |
| `autoloanctl` | Auto loans | `ltv`, `payment` |
| `persctl` | Personal loans | `eligible`, `payment` |
| `cardctl` | Credit cards | `limit`, `apr`, `transfer`, `cli` |
| `compctl` | Compliance | `trid`, `atr`, `adverse` |
| `auditctl` | Audit trail | `log`, `query`, `verify`, `export` |

## Usage Pattern
```bash
<tool> <command> --option value --format json
```

## Common Options
- `--format json` — Machine-readable output
- `--help` — Just-in-time documentation
- `--credit-score <n>` — FICO score
- `--income <n>` — Annual income
- `--amount <n>` — Loan amount

## Product Workflows

**Mortgage**: finctl → creditctl → mortctl → compctl → auditctl
**Auto**: finctl → creditctl → autoloanctl → auditctl
**Personal**: finctl → creditctl → persctl → compctl → auditctl
**Card**: creditctl → cardctl → auditctl
