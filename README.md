# chrome-x-not-brother-rules

**English** · [简体中文](README.zh-CN.md)

Community filter rules catalog and sharing repository for the [Not Brother (不是兄弟)](https://github.com/interjc/chrome-x-not-brother) Chrome extension.

---

## Table of Contents

- [Introduction](#introduction)
- [How to Use Rules](#how-to-use-rules)
  - [Method 1: Load via Raw URL directly (Recommended)](#method-1-load-via-raw-url-directly-recommended)
  - [Method 2: Download or Customize, then Upload to Import](#method-2-download-or-customize-then-upload-to-import)
- [Rule Catalog](#rule-catalog)
- [Authoring & Editing Rules](#authoring--editing-rules)
  - [Using AI Skill for Intelligent Editing (Recommended)](#using-ai-skill-for-intelligent-editing-recommended)
  - [Manual / Custom Rule Specifications](#manual--custom-rule-specifications)
- [Contributing & Sharing](#contributing--sharing)
- [Related Links](#related-links)
- [License](#license)

---

## Introduction

[Not Brother (不是兄弟)](https://github.com/interjc/chrome-x-not-brother) is a Chrome extension for X (formerly Twitter) that provides relationship status badges, local relationship archives, and timeline content filtering.

The extension supports JSON rule files adhering to the `not-brother-filter-rules` (v1) format, automatically hiding posts based on **account handles**, **display names**, or **post content keywords / regular expressions**.

This repository is used to collect, organize, and share community-curated high-quality rule sets.

---

## How to Use Rules

### Method 1: Load via Raw URL directly (Recommended)

1. In the extension, open the **Side Panel** or **Relationship Fieldbook**, and switch to the **Rules** tab.
2. Locate the **Import & Export** section.
3. In the **Load URL** input field, paste the GitHub Raw link of the desired rule file from this repository, for example:
   ```text
   https://github.com/interjc/chrome-x-not-brother-rules/raw/refs/heads/main/rules/filter-default.json
   ```
4. Click **Load URL** and choose the import mode:
   - **Update by ID** (Recommended): Replaces existing rules with matching `id`s, preserves other existing local rules, and appends any new rules from the file to the end.
   - **Overwrite all**: Clears all existing rules under the currently logged-in X account and replaces them entirely with the rules from the file.
5. In the Side Panel **Options** or **Rules** tab, ensure the **"Apply custom filter rules"** master switch is turned on.

> [!TIP]
> When loading public HTTPS URLs, the extension performs a one-time fetch only and does not poll or auto-refresh silently in the background. When community rules are updated, simply click Load URL again on the Rules page to sync the latest rules.

### Method 2: Download or Customize, then Upload to Import

1. Navigate to the target `.json` file under [`rules/`](rules/) in this repository and download it locally (or copy the contents and save as a `.json` file).
2. Add, remove, or modify keywords and handles according to your personal preferences.
3. In Not Brother under **Rules** -> **Import & Export**, click **Upload JSON file** and select your modified file to import.

---

## Rule Catalog

| File Path | Rule Set Name | Description | Raw Import Link |
| --- | --- | --- | --- |
| [`rules/filter-default.json`](rules/filter-default.json) | Default Rules | Universal filter rules covering common spam promotion, adult traffic keywords, and abnormal handles | [Raw Link](https://github.com/interjc/chrome-x-not-brother-rules/raw/refs/heads/main/rules/filter-default.json) |

*Pull requests contributing additional categorized rule sets (e.g., crypto scam filters, topic-specific anti-harassment, etc.) are warmly welcome!*

---

## Authoring & Editing Rules

### Using AI Skill for Intelligent Editing (Recommended)

Not Brother provides an official Agent Skill dedicated to rule authoring, editing, and validation. You can install this Skill into your AI coding assistant (such as Antigravity, Cursor, Claude Code, Copilot, etc.) to automatically generate, read, merge, validate, and optimize rule files according to official standards:

```bash
npx skills add https://github.com/interjc/chrome-x-not-brother --skill x-not-brother-rules
```

Once installed, you can prompt your AI directly, for example:
- *"Add a set of regex rules to filter crypto airdrop spam to filter-default.json"*
- *"Check if any regular expressions in this rule file have catastrophic backtracking (ReDoS) risks or syntax errors"*
- *"Merge these two rule JSON files by ID and normalize them"*

### Manual / Custom Rule Specifications

Rule files must strictly comply with the `not-brother-filter-rules` (v1) Schema specification:

```json
{
  "format": "not-brother-filter-rules",
  "schemaVersion": 1,
  "name": "Rule Set Name",
  "description": "Rule set description",
  "rules": [
    {
      "id": "rule-unique-id-1",
      "label": "Rule Label Name",
      "enabled": true,
      "expiresAt": null,
      "type": "content",
      "match": {
        "mode": "regex",
        "value": "keywordA|keywordB",
        "caseSensitive": false
      }
    },
    {
      "id": "rule-unique-id-2",
      "label": "Specific Account",
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

#### Key Field Constraints:
- **`format`**: Exactly `"not-brother-filter-rules"`.
- **`schemaVersion`**: Exactly `1`.
- **`rules[].id`**: 1–64 characters, starting with an alphanumeric character, matching `^[a-zA-Z0-9][a-zA-Z0-9.:_-]*$`. Must be unique within the file and remain stable across edits to enable "Update by ID".
- **`rules[].type`**: Supports 3 matching types:
  - `user_handles`: Match account handles (without `@`, lowercase, 1 to 10,000 handles).
  - `display_name`: Match account display name (nickname).
  - `content`: Match visible post body text.
- **`rules[].match`**: Used for `display_name` and `content` types:
  - `mode`: `"contains"` (substring match) or `"regex"` (JavaScript Unicode regular expression, do not wrap with slashes).
  - `caseSensitive`: Boolean, whether matching is case-sensitive.
- **`rules[].expiresAt`**: `null` for perpetual rules, or an ISO 8601 string with timezone (e.g., `"2027-01-01T00:00:00.000Z"`).
- **File size limits**: Max 1 MiB per JSON file, max 500 rules per file.

---

## Contributing & Sharing

If you have curated a useful set of filter rules, we warmly welcome your contributions to share with the community!

Please read the complete **[Contributing Guide (CONTRIBUTING.md)](CONTRIBUTING.md)** before submitting.

### Key Guidelines Summary:
1. **No Abuse & No Targeting**: Rules must not contain private information, credentials, targeted harassment, or overly broad patterns that cause false positives on normal tweets. Ensure regexes are safe against ReDoS.
2. **Recommended AI Skill**: Use `npx skills add https://github.com/interjc/chrome-x-not-brother --skill x-not-brother-rules` to assist with generation and validation.
3. **Mandatory Self-Testing**: Rules must be imported and tested in the Not Brother extension on actual X pages before submitting a PR to verify efficacy and prevent false positives.
4. **Standard GitHub Flow**: Fork -> Create branch -> Commit changes -> Open Pull Request.

See [CONTRIBUTING.md](CONTRIBUTING.md) for full details.

---

## Related Links

- [Not Brother Extension Repository](https://github.com/interjc/chrome-x-not-brother)
- [Download on Chrome Web Store](https://chromewebstore.google.com/detail/dioanbgbpklflgochbljdehpjidckgfd)
- [Filter Rules Specification & Technical Documentation (filter-rules.md)](https://github.com/interjc/chrome-x-not-brother/blob/main/docs/en/filter-rules.md)
- [Official Rule Editing Skill](https://github.com/interjc/chrome-x-not-brother/tree/main/skills/x-not-brother-rules)

---

## License

This project is open-sourced under the [MIT License](LICENSE).
