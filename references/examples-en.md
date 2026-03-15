# English Audit Examples

## Example 1: file-organizer-skill

# Skill Audit Report
- Skill: `file-organizer-skill`
- Source: public `openclaw/skills` repository
- Author: `1999azzar`
- Version: `1.0.0`
- Files reviewed: 4
  - `SKILL.md`
  - `_meta.json`
  - `scripts/organize.py`
  - `references/categories.md`

## Risk Level
- **Medium**

## Risk Type
- **Over-broad permission risk**
- **High-impact local operation risk**

## Install Recommendation
- **Installable, but only after tightening defaults, directory scope, and recursive behavior**

## Should It Be Pulled Local for Thorough Audit?
- **Yes**
- Why:
  - This is not a text-only skill; it includes an executable script
  - It performs real local file moves
  - The risk is driven less by malice and more by defaults and impact radius
  - This makes it a good candidate for full source-level review

## Post-Audit File Handling Recommendation
- **Keep as a research sample if needed**
- But:
  - do not leave it in a default active skill path
  - prefer an isolated audit / lab directory
- Why:
  - it has redesign value
  - its risk comes from impact, not obvious malicious intent

## Security Advice
- Make **dry-run / preview** the default mode
- Add **explicit confirmation** for high-risk directories such as `Desktop`, `Downloads`, and `Documents`
- Treat `--recursive` as a **high-risk option** and disable it by default
- Add a **suggestion-only mode** that does not move files automatically
- Strengthen rollback guidance and clearly document the limits of `undo`
- Avoid running it by default on large directories, home directories, repo directories, or sync folders

## If Redesigning: Redesign Advice and Approval Guidance
- **Worth redesigning before use**
- Possible redesigns:
  - preview-first instead of execute-first
  - tighter directory scope
  - extra confirmation for recursive mode
  - suggestion-only mode
  - more reliable rollback behavior
- **Ask the user first**
- Why:
  - redesign changes the intended usage model
  - even though it is not malicious, it directly affects real user files
  - the user should decide whether its automation-first behavior should be kept at all

## Reasoning
- No clear signs of malicious code, dirty code, credential theft, privilege escalation, covert exfiltration, or remote fetch-and-execute were found.
- However, the skill does perform real file moves and supports recursive scanning, which can significantly increase the impact radius.
- The main concern is not “the code is malicious,” but rather “the code has enough power to make large local changes, while the default safeguards are not strong enough.”
- That makes **Medium** the right rating:
  - not **High**, because no explicit malicious indicators were found
  - not **Low**, because it can still materially alter a user’s real file structure

## Inventory
- This is a local file-organization skill with an executable script
- Supports extension-based sorting
- Supports date-based sorting
- Supports `--dry-run`
- Supports `--undo`
- Supports `--recursive`
- Writes `organize_history.json`

## Spec Review
- Frontmatter is relatively simple
- The `description` explains the main purpose
- `references/categories.md` is a reasonable supporting reference
- But the risk messaging is too weak, especially around:
  - not clearly recommending dry-run first
  - not strongly warning about recursive behavior
  - not clearly stating which directories should not be touched casually

## Behavior Review
- The script uses `shutil.move(...)` to move files for real
- It creates destination directories automatically
- In recursive mode it walks the directory tree with `os.walk(...)`
- It writes `organize_history.json` after execution
- Undo depends on the integrity of the history file
- It is not dry-run by default
- No network access, remote download, privilege escalation, or secret-harvesting behavior was found

## Risk Signals
- Real file moves may happen immediately
- Recursive scanning expands impact
- It can restructure local files in bulk
- Rollback depends on a local history file
- Partial completion states are possible if failures happen mid-run
- Risk warnings in the skill text are not strong enough

---

## Example 2: stealth-browser-mcp (real local audit sample)

# Skill Audit Report
- Skill: `stealth-browser-mcp`
- Source: GitHub `brian-ln/stealth-browser-mcp`
- Author: `Brian Lloyd-Newberry`
- Version:
  - `package.json`: `0.0.1`
  - `index.ts`: `1.0.0`
- Files reviewed: 4
  - `package.json`
  - `README.md`
  - `index.ts`
  - `CLAUDE.md`

## Risk Level
- **Extreme**

## Risk Type
- **Abuse-enablement / detection-evasion risk**
- **Over-broad permission risk**
- **Supply-chain / dependency risk**
- **Engineering consistency / transparency risk**

## Install Recommendation
- **Do not install**

## Should It Be Pulled Local for Thorough Audit?
- **Already done, and this type should be source-audited by default**
- Why:
  - public-facing descriptions are not enough for this category
  - stealth / anti-detection is the core value proposition
  - the right judgment requires source review, dependency review, and minimal runtime validation together

## Post-Audit File Handling Recommendation
- **Keep isolated or delete it; do not leave it in a normal active skills directory**
- Safer options:
  - keep it only in an isolated research directory
  - delete the local copy if research is complete
- Why:
  - its core capability is stealth and detection evasion
  - even without direct credential theft, it is a poor fit for a normal daily agent environment

## Security Advice
- Do not install it into a daily-use agent environment
- Do not give it access to normal browser profiles, cookies, or logged-in sessions
- Do not combine it with proxy rotation, captcha handling, or session persistence in normal environments
- If research continues, prioritize extra review of:
  - `playwright-extra`
  - `puppeteer-extra-plugin-stealth`
  - browser launch behavior
  - deeper fingerprint spoofing and network handling behavior
- Review dependency vulnerabilities separately; do not underestimate the risk just because the main code file is small

## If Redesigning: Redesign Advice and Approval Guidance
- **Not recommended for direct redesign-and-deploy under its current goal**
- If redesign is considered at all, it should:
  - remove stealth / anti-detection / bypass behavior
  - reduce it to ordinary transparent browser automation
  - narrow it to research-only or screenshot-only behavior
- **Ask the user first**
- Why:
  - the issue is not merely aggressive defaults
  - the product positioning itself is high risk

## Reasoning
- Both the README and the code explicitly state:
  - stealth mode
  - bypass bot detections
  - modifies browser fingerprints
  - `puppeteer-extra-plugin-stealth`
- That means the risk is not incidental. The risky capability is the core feature.
- Local testing also surfaced two extra concerns:
  - the dependency chain has vulnerabilities
  - the project did not start cleanly in the current environment
- So this is not just a “usable with tighter controls” case. It is a **core high-risk capability with only moderate engineering trustworthiness**, which makes **Extreme** the right rating.

## Inventory
- Full repository was cloned locally
- Reviewed:
  - `package.json`
  - `README.md`
  - `index.ts`
  - `CLAUDE.md`
- Dependencies include:
  - `playwright`
  - `playwright-extra`
  - `puppeteer-extra-plugin-stealth`
  - `fastmcp`

## Spec Review
- The README and code are aligned on the stealth / anti-detection goal
- But engineering consistency is only moderate:
  - the version in `package.json` does not match the version in code
- That is not malicious by itself, but it lowers trust somewhat

## Behavior Review
- The code explicitly enables:
  - `chromium.use(StealthPlugin())`
- The exposed tool is `screenshot`
- It launches Chromium and navigates to the target URL
- Minimal local runtime validation was performed:
  - `npm install` succeeded
  - `npx tsx index.ts` failed to start cleanly
- The startup failure indicates the MCP server was not directly runnable in the current environment

## Risk Signals
- stealth automation
- anti-bot evasion
- browser fingerprint modification
- high-risk automation dependencies
- install audit reported 4 vulnerabilities (1 low / 2 moderate / 1 critical)
- version mismatch
- startup failure / uneven engineering maturity

---

## Example 3: @playwright/mcp (real local audit sample)

# Skill Audit Report
- Skill: `@playwright/mcp`
- Source: GitHub `microsoft/playwright-mcp`
- Author: Microsoft Corporation
- Version: `0.0.68`
- Files reviewed: 4
  - `README.md`
  - `packages/playwright-mcp/package.json`
  - `packages/playwright-mcp/index.js`
  - `packages/playwright-mcp/cli.js`

## Risk Level
- **High**

## Risk Type
- **Over-broad permission risk**
- **High-impact local operation risk**
- **Session / identity boundary risk**

## Install Recommendation
- **Installable, but only after configuring restrictions first**

## Should It Be Pulled Local for Thorough Audit?
- **Optional**
- Why:
  - This is an officially maintained project with relatively transparent code structure and intent
  - The README already documents all tools, configuration options, and security boundaries in detail
  - For general use, a documentation-level audit is sufficient
  - However, for production use or sensitive data handling, a full source-level review is still recommended

## Post-Audit File Handling Recommendation
- **Can be kept and used**
- But:
  - restrict default browser behavior (headless, isolated profile)
  - limit the allowed domain scope
  - do not reuse the daily primary browser state
- Why:
  - This is a genuinely useful browser automation tool
  - The risk comes primarily from usage patterns, not malicious code

## Security Advice
- **Explicitly configure `--isolated`** to avoid reusing daily browser profiles
- **Configure `--allowed-origins` or `--blocked-origins`** to restrict accessible domains
- **Do not enable `--extension` mode by default** unless explicitly needed to connect to an existing browser
- **Do not enable `--shared-browser-context` by default** to avoid sharing login state across sessions
- **Use `--storage-state` with caution**; if used, ensure the storage state file itself is secure
- **Do not grant `--allow-unrestricted-file-access`**; keep the default file access restrictions
- For sensitive scenarios, consider enabling `--secrets` configuration to prevent the LLM from directly handling credentials

## If Redesigning: Redesign Advice and Approval Guidance
- **Worth redesigning before use**
- Possible redesigns:
  - default to more conservative behavior (isolated, headless, domain-restricted)
  - add a configuration validation layer to prevent users from accidentally enabling high-risk options
  - add usage logging to record target domains and operation types for each browser session
- **Ask the user first**
- Why:
  - redesign changes default behavior and may affect existing workflows
  - whether to accept more conservative defaults should be decided by the user

## Reasoning
- This is not malicious code:
  - It comes from Microsoft
  - Code intent is transparent
  - Documentation clearly states security boundaries
- But it does have **high-impact capabilities**:
  - Can access arbitrary websites
  - Can operate the browser (click, type, upload files, etc.)
  - Can read and modify cookies and localStorage
  - Can save and restore browser session state
- The core risk is not "the code is bad," but "the capabilities are too strong, and misuse can cause problems."
- It is **not** centered on detection evasion, so it should not be rated **Extreme**.
- But it can genuinely manipulate the user's browser and sessions, so it cannot be lowered to **Medium**.
-综合判断：**High** is the appropriate rating.

## Inventory
- This is a browser automation MCP server
- Supports multiple browsers (chromium, firefox, webkit, chrome, edge)
- Supports three modes: isolated / persistent / extension
- Provides many tools:
  - Core automation: navigate, click, type, snapshot, screenshot, etc.
  - Storage management: cookie, localStorage, sessionStorage
  - Optional capabilities: vision, pdf, devtools, testing
- Supports rich configuration options:
  - Domain restrictions
  - Permission grants
  - Session isolation
  - Output modes

## Spec Review
- The README is very detailed, covering:
  - Installation methods
  - Configuration options
  - Tool listings
  - Security notes
- Explicitly states "Playwright MCP is **not** a security boundary"
- No evidence of deliberate obfuscation or downplaying of risks
- Engineering structure is clear, versions are consistent

## Behavior Review
- Core code exposes MCP interface via `createConnection`
- Browser operations are implemented through Playwright's native API
- No evidence of:
  - stealth / anti-detection plugins
  - fingerprint spoofing
  - proxy rotation
  - captcha bypass
- But it can indeed:
  - access arbitrary websites
  - read and modify browser storage
  - save and restore session state
  - upload and download files

## Risk Signals
- Can access arbitrary websites (unless explicitly configured otherwise)
- Can read and modify cookies and localStorage
- Can save and restore browser session state
- Supports connecting to existing browsers (extension mode)
- Supports shared browser context (sharedBrowserContext)
- Default configuration may not be conservative enough (depends on user configuration)

---

## Example 4: healthcheck (low-risk local tracker)

# Skill Audit Report
- **Skill:** `healthcheck`
- **Source:** Local workspace skill
- **Author:** Not specified
- **Version:** `1.0.2`
- **Files Reviewed:** 1
  - `SKILL.md`

## Risk Level
- **Low**

## Risk Type
- **Minimal local write risk**

## Install Recommendation
- **Safe to install and use**

## Should It Be Pulled Local for Thorough Audit?
- **Not necessary**
- Why:
  - The skill only writes to a local JSON file
  - No network access, no credentials, no external API calls
  - Code is simple and fully visible in the SKILL.md

## Post-Audit File Handling Recommendation
- **Can be kept in normal skills directory**
- Why:
  - Low-impact utility skill
  - No sensitive data handling
  - No external dependencies

## Security Advice
- Ensure the `{baseDir}` path is within a user-controlled directory
- Consider adding input validation for the `cups` number to avoid negative or non-numeric values
- Optionally add a `--dry-run` mode for testing

## If Redesigning: Redesign Advice and Approval Guidance
- **Minor improvements suggested, no approval needed**
- Possible improvements:
  - Add input validation
  - Add a simple query command (e.g., "show today's water")
  - Add data export capability

## Reasoning
- This is a simple local data tracking skill
- It only writes to a JSON file in the user's workspace
- No network, no credentials, no external API
- The worst-case impact is minor data corruption in a local file
- This is clearly **Low** risk

## Inventory
- Tracks water intake and sleep via JSON file
- Uses only Node.js built-in `fs` module
- Commands: add water, add sleep, add wake, view stats, update, delete

## Spec Review
- Documentation is clear and concise
- All commands are documented with examples
- No hidden behavior

## Behavior Review
- All operations are local file writes/reads
- No network access
- No credential handling
- No external API calls

## Risk Signals
- Writes to local file (low impact)
- No input validation on numeric fields
- No backup/rollback mechanism

---

## Example 5: obsidian-ontology-sync (medium-risk local automation)

# Skill Audit Report
- **Skill:** `obsidian-ontology-sync`
- **Source:** Local workspace skill
- **Author:** Not specified
- **Version:** `1.0.0`
- **Files Reviewed:** 1
  - `SKILL.md`

## Risk Level
- **Medium**

## Risk Type
- **High-volume local read/write risk**
- **Data extraction / transformation risk**

## Install Recommendation
- **Installable, but review extraction rules and feedback behavior first**

## Should It Be Pulled Local for Thorough Audit?
- **Recommended**
- Why:
  - The skill reads and writes significant amounts of local data
  - It extracts entities and relationships from markdown files
  - It can auto-modify files (future bidirectional sync)
  - Understanding the extraction logic is important before trusting it

## Post-Audit File Handling Recommendation
- **Can be kept, but review cron jobs and extraction scope**
- Why:
  - This is a useful automation skill
  - Risk comes from volume of file access, not malice
  - Should be configured carefully

## Security Advice
- Review the `config.yaml` before enabling automatic sync
- Ensure the Obsidian vault path is correctly scoped
- Disable `apply-feedback` auto-modification until behavior is understood
- Review cron jobs to ensure they run at expected intervals
- Consider running extraction in `--dry-run` mode first

## If Redesigning: Redesign Advice and Approval Guidance
- **Worth redesigning with safer defaults**
- Possible improvements:
  - Default to `--dry-run` for first sync
  - Add explicit confirmation before auto-modifying files
  - Add backup creation before any file modification
  - Add logging for all extraction and modification operations
- **Ask the user first** if auto-modification behavior is acceptable

## Reasoning
- This skill reads and writes large volumes of local markdown files
- It extracts structured data (entities, relationships) from notes
- It has a planned bidirectional sync feature that can auto-modify files
- The risk is not malice, but the **volume and scope of file access**
- It should be **Medium** because:
  - No network or credential risk
  - But significant local file access and potential auto-modification

## Inventory
- Syncs Obsidian notes to an ontology graph
- Extracts entities: Person, Organization, Project, Event, Task
- Extracts relationships: works_at, assigned_to, met_at, etc.
- Supports cron-based automatic sync (every 3 hours)
- Generates feedback reports and template suggestions

## Spec Review
- Documentation is very detailed
- Clearly explains extraction rules and feedback loop
- Mentions future bidirectional sync capability
- Could be clearer about default safety behavior

## Behavior Review
- Reads markdown files from configured Obsidian directories
- Extracts entities and writes to ontology storage (JSONL or SQLite)
- Generates feedback reports
- Future capability: auto-modify Obsidian notes from ontology insights
- No network access or credential handling

## Risk Signals
- High-volume local file access
- Automatic extraction and transformation
- Planned auto-modification of user files
- Cron-based automatic execution
- No explicit backup mechanism mentioned

---

## Example 6: imap-smtp-email-plus (high-risk credential handler)

# Skill Audit Report
- **Skill:** `imap-smtp-email-plus`
- **Source:** Local workspace skill
- **Author:** Not specified
- **Version:** Not specified
- **Files Reviewed:** 1
  - `SKILL.md`

## Risk Level
- **High**

## Risk Type
- **Credential handling risk**
- **External API access risk**
- **Privacy / data exfiltration risk**

## Install Recommendation
- **Installable, but only after secure credential configuration and scope limitation**

## Should It Be Pulled Local for Thorough Audit?
- **Recommended**
- Why:
  - This skill handles email credentials
  - It has full IMAP/SMTP access to user's mailbox
  - Understanding the credential storage and transmission is critical
  - Should verify no credential logging or exfiltration

## Post-Audit File Handling Recommendation
- **Can be kept, but credentials must be stored securely**
- Why:
  - This is a legitimate email management skill
  - Risk comes from credential handling and mailbox access scope
  - Must ensure `.env` file is properly secured (mode 600, not committed to git)

## Security Advice
- **Store credentials in `.env` with file mode 600**
- **Use app-specific passwords** (not main account password) for Gmail/Outlook
- **Enable 2FA** on email accounts before using this skill
- **Restrict IMAP access** to read-only if possible
- **Review SMTP sending scope** before allowing bulk email
- **Add logging** for all sent emails for audit trail
- **Consider adding a `--dry-run` mode** for send operations

## If Redesigning: Redesign Advice and Approval Guidance
- **Worth redesigning with stronger security defaults**
- Possible improvements:
  - Default to read-only IMAP mode
  - Add explicit confirmation before sending emails
  - Add rate limiting for bulk send operations
  - Add email sending logs for audit
  - Support OAuth2 instead of password-based auth where possible
- **Ask the user first** if read-only default and explicit send confirmation are acceptable

## Reasoning
- This skill handles email credentials (IMAP/SMTP)
- It has full access to user's mailbox (read, send, move, delete)
- Credentials are stored in `.env` file, which must be secured
- The risk is not malice, but the **sensitivity of credentials and mailbox access**
- It should be **High** because:
  - Credential handling is inherently sensitive
  - Full mailbox access could lead to privacy issues if misused
  - SMTP sending could be abused for spam if not controlled

## Inventory
- IMAP commands: check, fetch, download, search, mark-read, mark-unread, move, list-mailboxes
- SMTP commands: send, test
- Supports multiple email providers (Gmail, Outlook, 163.com, etc.)
- Requires `.env` file with credentials

## Spec Review
- Documentation is comprehensive
- Clearly lists supported providers and configuration
- Mentions security notes (app passwords, authorization codes)
- Could be more explicit about credential security requirements

## Behavior Review
- Connects to IMAP/SMTP servers using provided credentials
- Can read, search, and modify mailbox state
- Can send emails via SMTP
- No evidence of credential logging or exfiltration in documentation
- Requires careful credential storage

## Risk Signals
- Handles email credentials
- Full IMAP mailbox access (read, modify, delete)
- Full SMTP sending capability
- Credentials stored in `.env` file (must be secured)
- No OAuth2 support mentioned (uses password-based auth)
