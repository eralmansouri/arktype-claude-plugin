# ArkType Plugin Marketplace for Claude Code

A Claude Code plugin marketplace providing an [ArkType](https://arktype.io) skill — the TypeScript runtime validation library that's 100x faster than Zod.

## Installation

### 1. Add the marketplace

```bash
/plugin marketplace add eralmansouri/arktype-claude-plugin
```

### 2. Install the plugin

```bash
/plugin install arktype@arktype-claude-plugin
```

Or use the interactive menu:

```bash
/plugin
```

Then browse to "Discover" tab and select the arktype plugin.

### 3. Restart Claude Code

Restart to load the skill.

## What's Included

This plugin adds an ArkType **skill** that Claude automatically uses when you:

- Ask about ArkType
- Write validation schemas with `type()`
- Work with TypeScript runtime validation

### Skill Contents

- **SKILL.md** — Core ArkType syntax, keywords, constraints, morphs, scopes
- **references/keywords.md** — Complete keyword and fluent method tables
- **references/advanced.md** — Match, configuration, JSON Schema, integrations

## Usage Examples

Once installed, just ask Claude naturally:

- "Create an ArkType schema for a user with email and optional age"
- "How do I parse JSON and validate the result with ArkType?"
- "Add validation that rejects empty strings"

## License

MIT
