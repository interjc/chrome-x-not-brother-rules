# 贡献指南 (Contributing Guide)

感谢你对 [chrome-x-not-brother-rules](https://github.com/interjc/chrome-x-not-brother-rules) 的关注与支持！

本仓库是 [Not Brother（不是兄弟）](https://github.com/interjc/chrome-x-not-brother) 插件的社区规则分享与协作平台。为了保证社区规则集的质量、安全性和可用性，请在提交贡献前仔细阅读以下规范。

---

## 核心贡献规范

### 1. 严禁滥用与恶意行为 (No Abuse)

- **禁止恶意针对与霸凌**：不得利用本规则库恶意针对无辜用户、正常内容创作者或特定群体进行定向骚扰、抹黑或封锁。
- **禁止夹带私货与隐私泄露**：严禁提交包含个人隐私信息、敏感凭据（如 Token、密码、Cookie）、内部关键词或版权违规的内容。
- **避免过度宽泛与误伤**：编写内容（`content`）或昵称（`display_name`）规则时，严禁使用过于泛化的单字、通用词汇或贪婪匹配，避免造成大面积误杀正常推文。
- **确保正则安全 (ReDoS Prevention)**：所有正则表达式必须安全可控，严禁包含嵌套量词（如 `(a+)+`、`(.*)+`）等可能导致浏览器主线程卡死的灾难性回溯表达式。

### 2. 推荐使用官方 AI Skill 辅助编写与校验

为了确保规则严格符合 `not-brother-filter-rules` (v1) Schema，强烈推荐安装并使用官方提供的 Agent Skill：

```bash
npx skills add https://github.com/interjc/chrome-x-not-brother --skill x-not-brother-rules
```

该 Skill 能帮助你或你的 AI 编程助手（如 Antigravity, Cursor, Claude Code 等）自动完成：
- 严格按 Schema 规范格式化 JSON。
- 自动清理 Handle（去除 `@`，转为小写）。
- 正则表达式语法与 ReDoS 安全性检查。
- 保持规则 `id` 稳定，便于用户通过「按 ID 更新」无缝导入。

### 3. 必须在插件中亲测可用 (Mandatory Self-Testing)

**在提交 Pull Request 前，你必须亲自将修改后的规则文件导入到真实的 Not Brother 扩展中进行实测验证。**

#### 亲测验证步骤：
1. 打开 Not Brother 扩展，进入 **规则** -> **导入与导出**。
2. 使用 **上传 JSON 文件** 导入你修改后的规则文件（选择「清空后覆盖」或「按 ID 更新」）。
3. 确保侧栏或选项中的 **「应用自定义拦截规则」** 总开关已开启。
4. 打开 `x.com`（首页、搜索页、通知或帖子评论区），进行实际测试：
   - [ ] **有效性**：目标广告、引流、色流或骚扰帖子能够被正常隐藏。
   - [ ] **无误杀**：正常用户的推文与互动未受到误影响。
   - [ ] **流畅性**：页面浏览与滑动流畅，无卡顿、无控制台报错。

---

## GitHub 社区推荐提交流程 (GitHub Flow)

本仓库遵循标准的 GitHub 协作工作流：

### 第一步：Fork 仓库
点击本仓库右上角的 **Fork** 按钮，将仓库复制到你个人的 GitHub 账号下。

### 第二步：克隆并创建分支
在你的本地机器上克隆你的 Fork 仓库，并从 `main` 分支切出一个语义化的特性分支：

```bash
git clone https://github.com/<your-username>/chrome-x-not-brother-rules.git
cd chrome-x-not-brother-rules
git checkout -b feature/add-crypto-scam-rules
```

### 第三步：新增或修改规则
- 规则文件均存放于 `rules/` 目录下（例如 `rules/filter-default.json`，或新增分类规则如 `rules/filter-crypto-spam.json`）。
- 确保文件编码为 UTF-8，使用 2 格空格缩进，格式合法。
- 确保规则字段完整且符合规范（`id`, `label`, `enabled`, `expiresAt`, `type`, `match` / `handles`）。

### 第四步：本地验证与亲测
按照前文说明，将 JSON 文件上传至 Not Brother 插件，在 X 网页上实际验证拦截效果。

### 第五步：提交代码并推送
编写清晰、规范的 Git 提交信息：

```bash
git add rules/
git commit -m "feat(rules): add filter rules for crypto phishing spam"
git push origin feature/add-crypto-scam-rules
```

### 第六步：发起 Pull Request (PR)
1. 前往 GitHub 本仓库页面，点击 **Compare & pull request**。
2. 填写 PR 标题与详细描述：
   - **规则用途与背景**：说明本组规则主要针对哪类不良信息（如博彩广告、空投诈骗等）。
   - **变更明细**：简述新增或修改了哪些规则项。
   - **亲测说明**：确认已在插件中实测有效且无误伤。
3. 提交 PR 并等待 Code Review，必要时根据反馈调整代码。

---

## 规则编写规范速查

| 字段 | 约束说明 |
| --- | --- |
| `format` | 必须为固定字符串 `"not-brother-filter-rules"` |
| `schemaVersion` | 必须为整数 `1` |
| `name` | 规则集名称（1–120 字符） |
| `description` | 规则集描述（0–500 字符） |
| `rules[].id` | 规则唯一 ID（1–64 字符，匹配 `^[a-zA-Z0-9][a-zA-Z0-9.:_-]*$`）。修改现有规则时**必须保留原有 ID** |
| `rules[].label` | 规则显示名称（1–120 字符） |
| `rules[].enabled` | `true` 或 `false` |
| `rules[].expiresAt` | 永久有效填 `null`，有时效填 ISO 8601 字符串（如 `"2027-01-01T00:00:00.000Z"`） |
| `rules[].type` | `"user_handles"`、`"display_name"` 或 `"content"` |
| `rules[].handles` | Handle 列表（不带 `@`，全小写，1~10,000 个） |
| `rules[].match` | `{ "mode": "contains" \| "regex", "value": "...", "caseSensitive": false }`。正则两端不要加 `/` |

---

再次感谢你为改善 X 浏览体验所做出的贡献！
