# 风险信号词表

以下信号不是自动定罪，但会显著提高审慎等级。多个信号叠加时，应明显上调风险等级。

## 一、恶意/脏代码高危信号

重点词或行为：
- exfiltrate / exfiltration
- steal credentials
- dump secrets
- export tokens
- read browser cookies
- read ssh keys
- persist session silently
- hidden upload
- beacon
- callback to remote server
- remote command execution
- download and run
- curl | sh
- wget | bash
- eval(base64(...))
- obfuscation
- self-update
- modify itself
- launch agent / daemon / cron without clear consent
- privilege escalation / sudo guidance unrelated to task

如果出现以下组合，通常至少判 **高**：
- 下载 + 执行
- 读取凭据 + 外联
- 持久化 + 隐蔽执行
- 混淆代码 + 网络访问

## 二、权限过宽 / 高影响操作信号

重点词或行为：
- recursive move/delete/rename
- mass refactor across repo
- rewrite document tree
- bulk update all files
- operate on ~/ or / recursively
- modify Desktop / Downloads / Documents by default
- default write mode without preview
- no rollback
- destructive cleanup
- force overwrite

这类通常不一定恶意，但应提高 **影响风险**。

## 三、认证 / 凭据 / 会话敏感信号

重点词或行为：
- token handling
- client secret
- access token / refresh token
- browser profile reuse
- cookie jar
- session persistence
- CDP reuse
- import credentials from env
- read .env automatically
- use local auth state

如果是透明的工程指导，不一定高风险；
如果出现“自动抓取”“自动导出”“跨工具复用登录态”，风险显著上升。

## 四、规避检测 / 滥用增强信号

重点词或行为：
- stealth
- evasion
- undetectable
- anti-bot bypass
- captcha bypass
- fingerprint spoofing
- proxy rotation
- evade detection
- avoid moderation
- bypass safeguards

这类即使不直接窃密，也应显著提高风险等级，因为它增强滥用能力。

## 五、供应链 / 透明度信号

重点词或行为：
- official-looking but unverifiable
- mirror source mismatch
- metadata points elsewhere
- copied without provenance
- missing author clarity
- inconsistent repo / owner / commit
- page claims security but no auditable files
- too much trust in stars/downloads

这类更多影响**可信度**，常把“低”抬到“中”，或把“中”抬到“高”。

## 六、规范不足信号

重点词或行为：
- frontmatter fields bloated
- trigger description scattered in body
- giant SKILL.md with no references split
- README / CHANGELOG clutter
- vague promises like “works for everything”
- social engineering tone
- pressure to install quickly
- “trust me” style language

这类单独出现通常不是高危，但会降低整体可信度与可维护性。
