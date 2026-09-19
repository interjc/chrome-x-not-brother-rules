# Contributing Guide

**English** · [简体中文](CONTRIBUTING.zh-CN.md)

Thank you for your interest in and support for [chrome-x-not-brother-rules](https://github.com/interjc/chrome-x-not-brother-rules)!

This repository is the community sharing and collaboration platform for filter rules used by the [Not Brother (不是兄弟)](https://github.com/interjc/chrome-x-not-brother) Chrome extension. To maintain the quality, safety, and reliability of the community rule sets, please read the following guidelines carefully before contributing.

---

## Core Contribution Standards

### 1. No Abuse & Malicious Behavior

- **No Targeted Harassment or Bullying**: Do not use this rule catalog to maliciously target innocent users, legitimate content creators, or specific groups for coordinated harassment, defamation, or censorship.
- **No Covert Insertions or Privacy Leaks**: Strictly refrain from submitting content that includes personal private data, sensitive credentials (e.g., tokens, passwords, cookies), proprietary internal keywords, or copyrighted materials.
- **Avoid Overly Broad Catch-Alls & False Positives**: When authoring `content` or `display_name` rules, strictly avoid single characters, common generic words, or greedy patterns that cause massive false-positive hiding of normal tweets.
- **Ensure ReDoS Prevention**: All regular expressions must be safe and controlled. Strictly avoid nested quantifiers (such as `(a+)+` or `(.*)+`) that could trigger catastrophic backtracking and freeze the browser's main thread.

### 2. Recommended Official AI Skill for Authoring & Validation

To ensure rules strictly conform to the `not-brother-filter-rules` (v1) Schema, we strongly recommend installing and using the official Agent Skill:

```bash
npx skills add https://github.com/interjc/chrome-x-not-brother --skill x-not-brother-rules
```

This Skill assists you or your AI coding assistant (such as Antigravity, Cursor, Claude Code, etc.) to automatically:
- Format JSON strictly according to the Schema specification.
- Clean handles automatically (strip `@`, convert to lowercase).
- Check regex syntax and verify ReDoS safety.
- Keep rule `id`s stable so users can seamlessly import via "Update by ID".

### 3. Mandatory Self-Testing in the Extension

**Before submitting a Pull Request, you must personally import your modified rule file into the actual Not Brother extension and verify it on live X pages.**

#### Verification Steps:
1. Open the Not Brother extension, navigate to **Rules** -> **Import & Export**.
2. Use **Upload JSON file** to import your modified rule file (choose "Overwrite all" or "Update by ID").
3. Ensure the master switch **"Apply custom filter rules"** is turned on in the Side Panel or Options.
4. Open `x.com` (Home timeline, search, notifications, or reply threads) and test thoroughly:
   - [ ] **Efficacy**: Target spam, promo, adult, or harassment posts are properly hidden.
   - [ ] **No False Positives**: Normal users' tweets and interactions are unaffected.
   - [ ] **Smooth Performance**: Page browsing and scrolling remain smooth without lag or console errors.

---

## GitHub Community Recommended Workflow (GitHub Flow)

This repository follows standard GitHub collaboration workflow:

### Step 1: Fork the Repository
Click the **Fork** button in the top right of this repository to copy it to your personal GitHub account.

### Step 2: Clone and Create a Branch
Clone your fork locally and create a semantic feature branch off `main`:

```bash
git clone https://github.com/<your-username>/chrome-x-not-brother-rules.git
cd chrome-x-not-brother-rules
git checkout -b feature/add-crypto-scam-rules
```

### Step 3: Add or Modify Rules
- All rule files reside under the `rules/` directory (e.g., `rules/filter-default.json`, or a new categorized rule file like `rules/filter-crypto-spam.json`).
- Ensure UTF-8 encoding, 2-space indentation, and valid JSON syntax.
- Ensure all rule fields are complete and adhere to the schema (`id`, `label`, `enabled`, `expiresAt`, `type`, `match` / `handles`).

### Step 4: Local Verification and Self-Testing
As described above, upload the JSON file to Not Brother and test on live X pages.

### Step 5: Commit and Push Changes
Write a clear, standardized Git commit message:

```bash
git add rules/
git commit -m "feat(rules): add filter rules for crypto phishing spam"
git push origin feature/add-crypto-scam-rules
```

### Step 6: Open a Pull Request (PR)
1. Go to this repository on GitHub and click **Compare & pull request**.
2. Fill in the PR title and detailed description:
   - **Purpose and Background**: Describe what kind of unwanted content the rules target (e.g., gambling ads, airdrop phishing, etc.).
   - **Changes**: Briefly describe which rules were added or updated.
   - **Self-Testing Confirmation**: Confirm that you tested the rules in the extension and verified efficacy without false positives.
3. Submit the PR and await Code Review, adjusting if feedback is provided.

---

## Rule Authoring Quick Reference

| Field | Constraints & Description |
| --- | --- |
| `format` | Must be exactly `"not-brother-filter-rules"` |
| `schemaVersion` | Must be the integer `1` |
| `name` | Rule set name (1–120 characters) |
| `description` | Rule set description (0–500 characters) |
| `rules[].id` | Unique rule ID (1–64 characters, matching `^[a-zA-Z0-9][a-zA-Z0-9.:_-]*$`). **Must preserve existing ID** when modifying existing rules |
| `rules[].label` | User-facing rule label (1–120 characters) |
| `rules[].enabled` | `true` or `false` |
| `rules[].expiresAt` | `null` for perpetual rules, or an ISO 8601 string with timezone for time-limited rules (e.g., `"2027-01-01T00:00:00.000Z"`) |
| `rules[].type` | `"user_handles"`, `"display_name"`, or `"content"` |
| `rules[].handles` | Handle array (without `@`, lowercase, 1 to 10,000 handles) |
| `rules[].match` | `{ "mode": "contains" \| "regex", "value": "...", "caseSensitive": false }`. Do not wrap regex with `/` |

---

Thank you again for contributing to a cleaner X browsing experience!
