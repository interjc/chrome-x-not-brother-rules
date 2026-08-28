# chrome-x-not-brother-rules

[Not Brother（不是兄弟）](https://github.com/interjc/chrome-x-not-brother) Chrome 扩展的自定义拦截规则（Filter Rules）社区分享与协作仓库。

---

## 目录

- [简介](#简介)
- [如何使用规则](#如何使用规则)
  - [方式一：直接加载 Raw URL（推荐）](#方式一直接加载-raw-url推荐)
  - [方式二：下载或自定义后上传导入](#方式二下载或自定义后上传导入)
- [规则库列表](#规则库列表)
- [编辑与制作规则](#编辑与制作规则)
  - [使用 AI Skill 智能编辑（推荐）](#使用-ai-skill-智能编辑推荐)
  - [手写 / 自定义修改规则规范](#手写--自定义修改规则规范)
- [参与贡献与分享](#参与贡献与分享)
- [相关链接](#相关链接)
- [开源协议](#开源协议)

---

## 简介

[Not Brother（不是兄弟）](https://github.com/interjc/chrome-x-not-brother) 是一款面向 X（原 Twitter）的 Chrome 扩展，提供关系状态标注、本地关系档案库以及时间线内容过滤等功能。

扩展支持通过 `not-brother-filter-rules` (v1) 格式的 JSON 规则文件，按照 **账号 Handle**、**用户显示名称** 或 **帖子正文关键词/正则** 自动隐藏指定内容。

本仓库用于收集、整理并分享社区维护的优质规则集。

---

## 如何使用规则

### 方式一：直接加载 Raw URL（推荐）

1. 在扩展中打开 **侧栏** 或 **关系档案库**，切换到 **规则** 标签页。
2. 找到 **导入与导出** 区域。
3. 在 **加载地址** 输入框中，填入本仓库对应规则文件的 GitHub Raw 链接，例如：
   ```text
   https://github.com/interjc/chrome-x-not-brother-rules/raw/refs/heads/main/rules/filter-default.json
   ```
4. 点击 **加载地址**，选择导入模式：
   - **按 ID 更新**（推荐）：整条替换相同 `id` 的规则，保留你本地已有的其他规则，并将文件里的新规则追加到列表末尾。
   - **清空后覆盖**：清除当前登录 X 账号下的全部现有规则，完全替换为该文件的规则。
5. 在侧栏 **选项** 或 **规则** 中确认已开启 **「应用自定义拦截规则」** 总开关。

> [!TIP]
> 扩展加载公开 HTTPS 地址时仅执行单次拉取，不会在后台静默轮询或自动刷新。如果社区规则更新，只需在规则页重新点击加载即可同步最新内容。

### 方式二：下载或自定义后上传导入

1. 点击本仓库 [`rules/`](rules/) 目录下的目标 `.json` 文件并下载到本地（或复制内容保存为 `.json`）。
2. 可根据个人需求自行增删修改关键词或 Handle。
3. 在 Not Brother 的 **规则** -> **导入与导出** 中点击 **上传 JSON 文件**，选择修改好的文件导入。

---

## 规则库列表

| 文件路径 | 规则名称 | 说明 | Raw 导入链接 |
| --- | --- | --- | --- |
| [`rules/filter-default.json`](rules/filter-default.json) | 默认规则库 | 包含常见引流、广告、博彩色流关键词与异常 Handle 的通用过滤规则 | [Raw Link](https://github.com/interjc/chrome-x-not-brother-rules/raw/refs/heads/main/rules/filter-default.json) |

*欢迎提交 Pull Request 补充更多分类规则（如垃圾广告过滤、特定领域防骚扰等）！*

---

## 编辑与制作规则

### 使用 AI Skill 智能编辑（推荐）

Not Brother 官方提供了专用于规则编辑与校验的 Agent 技能（Skill）。你可以将该 Skill 添加到你的 AI 编程助手（如 Antigravity, Cursor, Claude Code, Copilot 等）中，让 AI 按照官方规范自动生成、阅读、合并、校验和优化规则文件：

```bash
npx skills add https://github.com/interjc/chrome-x-not-brother/tree/main/skills/x-not-brother-rules
```

安装后，你可以直接对 AI 下达指令，例如：
- *“帮我往 filter-default.json 中添加一组过滤币圈空投引流推文的正则规则”*
- *“检查这份规则文件中的正则表达式是否存在回溯风险或语法错误”*
- *“将这两个规则 JSON 按 ID 合并并规范化”*

### 手写 / 自定义修改规则规范

规则文件必须满足 `not-brother-filter-rules` (v1) Schema 规范：

```json
{
  "format": "not-brother-filter-rules",
  "schemaVersion": 1,
  "name": "规则集名称",
  "description": "规则集描述",
  "rules": [
    {
      "id": "rule-unique-id-1",
      "label": "规则标签名称",
      "enabled": true,
      "expiresAt": null,
      "type": "content",
      "match": {
        "mode": "regex",
        "value": "关键词A|关键词B",
        "caseSensitive": false
      }
    },
    {
      "id": "rule-unique-id-2",
      "label": "指定账号",
      "enabled": true,
      "expiresAt": null,
      "type": "user_handles",
      "handles": [
        "spambot_account"
      ]
    }
  ]
}
```

#### 规则关键字段约束：
- **`format`**：固定为 `"not-brother-filter-rules"`。
- **`schemaVersion`**：固定为数字 `1`。
- **`rules[].id`**：1–64 字符，以字母或数字开头，可包含字母、数字、`.`、`:`、`_`、`-`。必须在文件内唯一且保持稳定，便于「按 ID 更新」。
- **`rules[].type`**：支持 3 种匹配类型：
  - `user_handles`：匹配账号 Handle（不带 `@`，全小写，支持 1~10,000 个）。
  - `display_name`：匹配账号显示名称（昵称）。
  - `content`：匹配帖子可见正文。
- **`rules[].match`**：用于 `display_name` 和 `content` 类型：
  - `mode`：`"contains"`（包含文本）或 `"regex"`（JavaScript Unicode 正则表达式，两端不要加 `/`）。
  - `caseSensitive`：布尔值，是否区分大小写。
- **`rules[].expiresAt`**：`null` 表示永久有效，或填入带时区的 ISO 8601 字符串（如 `"2027-01-01T00:00:00.000Z"`）。
- **文件体积限制**：单个 JSON 文件不超过 1 MiB，规则数量不超过 500 条。

---

## 参与贡献与分享

如果你整理了一套好用的拦截规则，非常欢迎贡献到本仓库与大家分享！

### 贡献流程

1. **Fork 本仓库** 到你自己的 GitHub 账号。
2. 在 `rules/` 目录下新增规则文件（如 `rules/filter-crypto-spam.json`）或改进已有规则文件。
3. **自测与规范检查**：
   - 确保 JSON 格式合法并通过 `not-brother-filter-rules` v1 规范验证。
   - 确保正则表达式安全，避免灾难性回溯（ReDoS）及范围过宽导致的误杀。
   - 确保规则中**不包含任何个人隐私信息、私密关键词或敏感凭据**。
4. 提交 Pull Request，并在 PR 描述中简要说明规则集的用途和适用场景。

---

## 相关链接

- [Not Brother 插件主仓库](https://github.com/interjc/chrome-x-not-brother)
- [Chrome 网上应用店下载](https://chromewebstore.google.com/detail/dioanbgbpklflgochbljdehpjidckgfd)
- [规则编写规范与技术文档 (filter-rules.md)](https://github.com/interjc/chrome-x-not-brother/blob/main/docs/filter-rules.md)
- [官方规则编辑 Skill](https://github.com/interjc/chrome-x-not-brother/tree/main/skills/x-not-brother-rules)

---

## 开源协议

本项目基于 [MIT License](LICENSE) 开源。
