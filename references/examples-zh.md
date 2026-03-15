# 中文审计样例

## 样例一：file-organizer-skill

# 技能审计报告
- 技能：`file-organizer-skill`
- 来源：公开 `openclaw/skills` 仓库
- 作者：`1999azzar`
- 版本：`1.0.0`
- 已检查文件：4
  - `SKILL.md`
  - `_meta.json`
  - `scripts/organize.py`
  - `references/categories.md`

## 风险等级
- **中**

## 风险类型
- **权限过宽风险**
- **高影响本地操作风险**

## 是否建议安装
- **可安装，但建议先限制默认行为、目录范围和递归权限后再使用**

## 是否建议拉到本地做彻底审计
- **建议**
- 原因：
  - 它不是纯文本 skill，而是带真实执行脚本
  - 会实际移动本地文件
  - 风险主要不在恶意，而在默认行为和影响范围
  - 这类 skill 很适合做一轮源码级 thorough 审计，确认还有没有边界情况

## 审计后文件处理建议
- **可保留样本继续研究**
- 但建议：
  - 不要放在常用技能目录的默认启用路径里
  - 最好单独放在审计/实验目录
- 原因：
  - 这类 skill 有改造价值
  - 风险来自高影响，而不是明显恶意

## 安全建议
- 将默认模式改为 **dry-run / preview**
- 对 `Desktop`、`Downloads`、`Documents` 等高风险目录增加**二次确认**
- 将 `--recursive` 标记为**高风险选项**并默认关闭
- 增加“**仅建议、不自动移动**”模式
- 强化回滚说明，明确 `undo` 的依赖条件和失败边界
- 不建议默认对大目录、用户主目录、仓库目录或同步目录直接运行

## 如需改造：改造建议与是否请示
- **值得改造后再用**
- 可改造方向：
  - 默认只预览，不直接执行
  - 限制可操作目录范围
  - 对递归模式单独加确认
  - 增加“只输出整理建议”的模式
  - 提升回滚机制的可靠性
- **建议先请示**
- 原因：
  - 改造会改变 skill 的默认行为和使用方式
  - 虽然不是恶意 skill，但它直接影响用户真实文件结构
  - 是否保留“自动整理”定位，应由用户决定

## 判定原因
- 没有发现明显恶意代码、脏代码、凭据窃取、提权、隐蔽外联或远程下载执行等信号，因此**不应误判为高风险恶意 skill**。
- 但它会真实移动用户文件，而且支持递归扫描，影响范围可能明显扩大。
- 这里的核心问题不是“代码本身坏”，而是“代码本身不坏，但默认能做的事情影响太大”。
- 所以它更适合归类为：
  - **高影响**
  - **高权限**
  - **需改造后更稳**
- 这也是为什么它判 **中**，而不是 **高**：
  - 没看到明显恶意 → 不上调到高
  - 真实会大量改文件 → 不能降到低

## 清单
- 这是一个带脚本的本地文件整理 skill
- 支持按扩展名分类
- 支持按日期分类
- 支持 `--dry-run`
- 支持 `--undo`
- 支持 `--recursive`
- 会写入 `organize_history.json`

## 规范检查
- frontmatter 相对简洁
- `description` 能说明主要用途
- `references/categories.md` 的存在合理
- 但风险提示明显不够，尤其是：
  - 没强调默认应先 dry-run
  - 没把 recursive 的高影响写重
  - 没写清哪些目录不建议直接动

## 行为检查
- 脚本会调用 `shutil.move(...)` 真实移动文件
- 会自动创建目标目录
- 递归模式下会 `os.walk(...)` 深扫目录
- 执行后会写 `organize_history.json`
- `undo` 依赖 history 文件做回滚
- 默认不是 dry-run
- 未发现网络访问、提权、secret 抓取、远程下载等行为

## 风险信号
- 默认可能直接执行真实文件移动
- 支持递归扫描，影响范围扩大
- 会批量改动本地文件结构
- 回滚依赖本地 history 文件
- 失败时可能留下部分完成状态
- 风险提示不足

---

## 样例二：stealth-browser-mcp（真实本地审计样本）

# 技能审计报告
- 技能：`stealth-browser-mcp`
- 来源：GitHub `brian-ln/stealth-browser-mcp`
- 作者：`Brian Lloyd-Newberry`
- 版本：
  - `package.json`: `0.0.1`
  - `index.ts`: `1.0.0`
- 已检查文件：4
  - `package.json`
  - `README.md`
  - `index.ts`
  - `CLAUDE.md`

## 风险等级
- **极高**

## 风险类型
- **滥用增强/规避检测风险**
- **权限过宽风险**
- **供应链/依赖风险**
- **说明不透明/工程一致性不足风险**

## 是否建议安装
- **不建议安装**

## 是否建议拉到本地做彻底审计
- **已执行，且这种类型本来就应做源码级彻底审计**
- 原因：
  - 只看公开介绍页不足以判断这类 skill
  - 它的核心卖点就是 stealth / anti-detection
  - 必须结合源码、依赖和最小化运行验证一起判断

## 审计后文件处理建议
- **建议隔离保留或直接删除，不要留在常用技能目录**
- 更稳妥的做法是：
  - 继续研究时隔离保留
  - 不再研究时删除本地副本
- 原因：
  - 这类项目的核心能力就是规避检测与隐蔽自动化
  - 即使没有直接窃密代码，也不适合放在日常 agent 环境中长期待命

## 安全建议
- 不要把它装进日常 agent 环境
- 不要给它共享日常浏览器 profile、cookie、登录态
- 不要和代理轮换、验证码处理、会话持久化联用
- 如果继续研究，优先再审：
  - `playwright-extra`
  - `puppeteer-extra-plugin-stealth`
  - 浏览器启动参数
  - 是否存在更深的指纹伪装和网络层处理
- 对依赖漏洞做单独审查，不要因为 skill 本身只有一个 `index.ts` 就低估整体风险

## 如需改造：改造建议与是否请示
- **不建议直接沿当前目标改造后上线使用**
- 如果真的要改造，方向应该是：
  - 去掉 stealth / anti-detection / bypass 能力
  - 缩回普通透明浏览器自动化
  - 限制成研究型或截图型能力
- **必须先请示**
- 原因：
  - 它的问题不只是默认太激进
  - 而是产品定位本身就高风险

## 判定原因
- 源码和 README 都明确写了：
  - stealth mode
  - bypass bot detections
  - modifies browser fingerprints
  - `puppeteer-extra-plugin-stealth`
- 这说明风险不是附带，而是核心卖点本身。
- 本地测试又补充暴露了两层额外问题：
  - 依赖链存在漏洞
  - 工程启动状态不稳
- 所以它不是“可用但需限制”的中高风险工具，而是**核心能力高风险 + 工程可信度一般**的样本，判 **极高** 是稳的。

## 清单
- 本地 clone 了完整仓库
- 已检查：
  - `package.json`
  - `README.md`
  - `index.ts`
  - `CLAUDE.md`
- 项目依赖包含：
  - `playwright`
  - `playwright-extra`
  - `puppeteer-extra-plugin-stealth`
  - `fastmcp`

## 规范检查
- README 与代码主旨一致，明确强调 stealth / anti-detection
- 但工程一致性一般：
  - `package.json` 与代码中的版本号不一致
- 这不等于恶意，但会降低可信度

## 行为检查
- 代码明确启用了：
  - `chromium.use(StealthPlugin())`
- 暴露的工具是 `screenshot`
- 会启动 Chromium 并访问目标 URL
- 当前已做最小化本地运行验证：
  - `npm install` 成功
  - `npx tsx index.ts` 启动失败
- 启动失败错误表明：
  - MCP server 在当前环境下未直接正常跑通

## 风险信号
- stealth automation
- anti-bot evasion
- browser fingerprint modification
- 依赖链包含高风险自动化组件
- 依赖安装后报告 4 个漏洞（1 low / 2 moderate / 1 critical）
- 版本号不一致
- 启动失败，工程成熟度一般

---

## 样例三：@playwright/mcp（真实本地审计样本）

# 技能审计报告
- 技能：`@playwright/mcp`
- 来源：GitHub `microsoft/playwright-mcp`
- 作者：Microsoft Corporation
- 版本：`0.0.68`
- 已检查文件：4
  - `README.md`
  - `packages/playwright-mcp/package.json`
  - `packages/playwright-mcp/index.js`
  - `packages/playwright-mcp/cli.js`

## 风险等级
- **高**

## 风险类型
- **权限过宽风险**
- **高影响本地操作风险**
- **会话/身份边界风险**

## 是否建议安装
- **可安装，但必须先配置限制条件后再使用**

## 是否建议拉到本地做彻底审计
- **可选**
- 原因：
  - 这是官方维护项目，代码结构和意图相对透明
  - README 中已详细列出所有工具、配置项和安全边界
  - 如果没有特殊研究需求，基于公开文档做审计已经足够
  - 但如果要用于生产环境或处理敏感数据，仍建议做源码级终审

## 审计后文件处理建议
- **可保留样本继续使用**
- 但建议：
  - 限制其默认浏览器行为（headless、隔离 profile）
  - 限制可访问的域名范围
  - 不复用日常主浏览器状态
- 原因：
  - 这是一个有真实价值的浏览器自动化工具
  - 风险主要来自使用方式，而不是代码本身恶意

## 安全建议
- **必须显式配置 `--isolated`**，避免复用日常浏览器 profile
- **必须配置 `--allowed-origins` 或 `--blocked-origins`**，限制可访问的域名
- **不要默认开启 `--extension` 模式**，除非明确需要连接现有浏览器
- **不要默认开启 `--shared-browser-context`**，避免多会话共享登录态
- **谨慎使用 `--storage-state`**，如要使用，确保存储状态文件本身是安全的
- **不要给它 `--allow-unrestricted-file-access`**，保持默认的文件访问限制
- 如用于敏感场景，建议启用 `--secrets` 配置，避免 LLM 直接接触认证信息

## 如需改造：改造建议与是否请示
- **值得改造后使用**
- 可改造方向：
  - 默认配置改为更保守的行为（isolated、headless、限制域名）
  - 增加一层配置校验逻辑，确保用户不会误开高风险选项
  - 增加使用日志，记录每次浏览器会话的目标域名和操作类型
- **建议先请示**
- 原因：
  - 改造会改变默认行为，可能影响现有工作流
  - 是否接受更保守的默认设置，应由用户决定

## 判定原因
- 这不是恶意代码：
  - 来自 Microsoft 官方
  - 代码意图透明
  - 文档清晰说明安全边界
- 但它确实有**高影响能力**：
  - 可以访问任意网站
  - 可以操作浏览器（点击、输入、上传文件等）
  - 可以读取/修改 cookie 和 localStorage
  - 可以保存/恢复浏览器会话状态
- 核心风险不是"代码本身坏"，而是"能力太强，用不好会出问题"
- 它**不是**以规避检测为核心卖点，所以不应判为**极高**
- 但它确实能真实操作用户的浏览器和会话，所以不能降到**中**
- 综合判断：**高** 是合适的

## 清单
- 这是一个浏览器自动化 MCP 服务器
- 支持多种浏览器（chromium、firefox、webkit、chrome、edge）
- 支持 isolated / persistent / extension 三种模式
- 提供大量工具：
  - 核心自动化：navigate、click、type、snapshot、screenshot 等
  - 存储管理：cookie、localStorage、sessionStorage
  - 可选能力：vision、pdf、devtools、testing
- 支持丰富的配置选项：
  - 域名限制
  - 权限授予
  - 会话隔离
  - 输出模式

## 规范检查
- README 非常详细，覆盖了：
  - 安装方式
  - 配置选项
  - 工具列表
  - 安全说明
- 明确声明 "Playwright MCP is **not** a security boundary"
- 没有发现刻意隐藏或模糊化风险的行为
- 工程结构清晰，版本一致

## 行为检查
- 核心代码通过 `createConnection` 暴露 MCP 接口
- 浏览器操作通过 Playwright 原生 API 实现
- 没有发现：
  - stealth / anti-detection 插件
  - 指纹伪装
  - 代理轮换
  - 验证码绕过
- 但它确实能：
  - 访问任意网站
  - 读取/修改浏览器存储
  - 保存/恢复会话状态
  - 上传/下载文件

## 风险信号
- 可以访问任意网站（除非显式配置限制）
- 可以读取/修改 cookie 和 localStorage
- 可以保存/恢复浏览器会话状态
- 支持连接现有浏览器（extension 模式）
- 支持共享浏览器上下文（sharedBrowserContext）
- 默认配置可能不够保守（取决于用户如何配置）
