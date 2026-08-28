# Repository instructions: chrome-x-not-brother-rules

## Repository role & purpose

This repository is the official community sharing and rule-set catalog for [Not Brother (`chrome-x-not-brother`)](https://github.com/interjc/chrome-x-not-brother), a Chrome Manifest V3 extension for X (formerly Twitter).

Rule sets are JSON documents located under the `rules/` directory (e.g., `rules/filter-default.json`). Users import these rule sets into the extension either by:
1. Entering the GitHub Raw URL in the extension's **Rules -> Import & Export -> Load URL** (`加载地址`).
2. Downloading / modifying the JSON file and uploading via **Upload JSON file** (`上传 JSON 文件`).

When modifying, generating, or validating rule sets in this repository, agents must strictly conform to the `not-brother-filter-rules` (v1) schema and safety rules.

---

## Authoritative Skill

For interactive rule editing and validation, the official skill is hosted in the main extension repository:

```bash
npx skills add https://github.com/interjc/chrome-x-not-brother/tree/main/skills/x-not-brother-rules
```

Agents handling rule generation or updates should refer to the schema and behavioral invariants below.

---

## Schema specification (`not-brother-filter-rules` v1)

Every JSON file in `rules/` must adhere to the following schema:

### Top-level document

```json
{
  "format": "not-brother-filter-rules",
  "schemaVersion": 1,
  "name": "Default Filter Rules",
  "description": "Community curated filter rules for spam, ads, and unwanted accounts.",
  "rules": []
}
```

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `format` | `string` | Exactly `"not-brother-filter-rules"` | Document format identifier |
| `schemaVersion` | `integer` | Exactly `1` | Schema version number |
| `name` | `string` | 1–120 characters, non-empty | Name of the rule set |
| `description` | `string` | 0–500 characters | Optional description (default `""`) |
| `rules` | `array` | Max 500 rules | Array of rule objects; `id` unique across the file |
| File size | - | Max 1 MiB UTF-8 | Total file size limit |

*Unknown or extraneous top-level fields are rejected by the extension.*

---

### Common rule fields

| Field | Type | Constraints | Description |
| --- | --- | --- | --- |
| `id` | `string` | 1–64 characters; matches `^[a-zA-Z0-9][a-zA-Z0-9.:_-]*$` | Unique rule ID. Must remain stable across edits to enable "Update by ID" |
| `label` | `string` | 1–120 characters | User-facing descriptive rule name |
| `enabled` | `boolean` | `true` or `false` | Whether the rule is actively applied |
| `expiresAt` | `string \| null` | `null` or ISO 8601 with timezone (e.g., `"2027-01-01T00:00:00.000Z"`) | `null` for perpetual rules; timestamp for time-limited rules |
| `type` | `string` | `"user_handles"`, `"display_name"`, or `"content"` | Discriminated union type |

---

### Rule types & matching

#### 1. `user_handles`

Hides posts authored by specific X accounts.

```json
{
  "id": "rule-spam-accounts-01",
  "label": "Known Spambot Accounts",
  "enabled": true,
  "expiresAt": null,
  "type": "user_handles",
  "handles": [
    "spambot1",
    "spambot2"
  ]
}
```

- `handles`: Array of 1 to 10,000 strings.
- Each handle must be clean (without `@`), lowercase, valid X username (1–15 alphanumeric / underscore characters).
- Unique within the `handles` array.

#### 2. `display_name`

Hides posts authored by accounts whose display name (nickname) matches the rule.

```json
{
  "id": "rule-name-keyword-01",
  "label": "Spam Display Name Pattern",
  "enabled": true,
  "expiresAt": null,
  "type": "display_name",
  "match": {
    "mode": "regex",
    "value": "高返币|se播|找主人|固炮",
    "caseSensitive": false
  }
}
```

- `match.mode`: `"contains"` or `"regex"`.
- `match.value`: 1–256 characters.
- `match.caseSensitive`: `boolean`.

#### 3. `content`

Hides posts whose visible post body text matches the rule.

```json
{
  "id": "rule-content-keyword-01",
  "label": "Promotion Post Filter",
  "enabled": true,
  "expiresAt": null,
  "type": "content",
  "match": {
    "mode": "regex",
    "value": "比我骚|我的福|主页能打",
    "caseSensitive": false
  }
}
```

- `match.mode`: `"contains"` or `"regex"`.
- `match.value`: 1–256 characters.
- `match.caseSensitive`: `boolean`.

---

## Regex safety & quality guidelines

When authoring `regex` match values:

1. **No wrapping slashes**: Provide only the pattern body (e.g. `foo|bar`, **not** `/foo|bar/` or `/foo|bar/i`).
2. **ReDoS prevention**: Avoid nested quantifiers and catastrophic backtracking (e.g., `(a+)+`, `(a|a+)+`, `.*.*`). Expressions must be safe for execution in the browser.
3. **Avoid broad catch-alls**: Do not match single generic characters or overly generic words that cause false positives on normal tweets.
4. **Unicode support**: Matching uses JavaScript Unicode semantics; text is normalized with Unicode NFKC before evaluation.
5. **Prefer `contains` when possible**: If simply matching a single static substring or exact phrase without alternation, use `"mode": "contains"`.

---

## Agent workflow & modification protocol

1. **Always emit complete, valid JSON**: When editing or creating files in `rules/`, write the entire file formatted with 2-space indentation. Never leave trailing commas or broken syntax.
2. **Preserve existing rule IDs**: Never regenerate existing rule `id`s when editing labels or patterns. Stable IDs allow extension users to use **按 ID 更新 (Update by ID)** smoothly.
3. **Generate valid IDs for new rules**: For new rules, generate an ID conforming to `^[a-zA-Z0-9][a-zA-Z0-9.:_-]*$` (e.g. `rule-<24_hex_chars>` like `rule-79bebce123914dbc919c2269` or semantic prefix like `rule-spam-01`).
4. **Privacy & Security**: Never commit real user credentials, private personal accounts, or confidential search keywords to this public repository.
5. **No build step required**: This repository contains static JSON rule files and documentation. Keep JSON files clean, standard, and directly fetchable over GitHub Raw.
