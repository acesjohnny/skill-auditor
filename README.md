# Skill Auditor

Audit OpenClaw/Claude/OpenAI-style skills for security, specification compliance, and installability.

## What It Does

Before installing or using a skill, `skill-auditor` helps you determine whether it is:
- **Safe to install**
- **Safe but requires tighter permissions/scope**
- **Malicious or high-risk, not recommended**

## Features

- Produces risk-assessed audit reports with clear reasoning
- Distinguishes between **malicious code risks** and **high-permission/high-impact risks**
- Supports both Chinese and English reports (follows user's query language)
- Provides actionable security recommendations
- Includes 6+ calibrated audit examples across Low/Medium/High/Extreme risk levels

## Usage

Ask the auditor to audit a skill:

```
请审计一下这个 skill: [skill 路径或 GitHub 链接]
```

The auditor will:
1. List all files in the skill
2. Check for malicious code signals
3. Check for high-permission/high-impact risks
4. Produce a formal audit report with:
   - Risk level (极低 / 低 / 中 / 高 / 极高)
   - Risk type
   - Install recommendation
   - Security recommendations
   - Detailed reasoning

## Risk Levels

| Level | Meaning |
|-------|---------|
| 极低 | Minimal risk, safe to install |
| 低 | Low risk, minor concerns |
| 中 | Medium risk, requires configuration changes |
| 高 | High risk, significant concerns, use with caution |
| 极高 | Extreme risk, not recommended for installation |

## Examples

The skill includes 6+ calibrated audit examples:
- `healthcheck` (Low)
- `file-organizer-skill` (Medium)
- `obsidian-ontology-sync` (Medium)
- `@playwright/mcp` (High)
- `imap-smtp-email-plus` (High)
- `stealth-browser-mcp` (Extreme)

## License

MIT

## Author

Johnny
