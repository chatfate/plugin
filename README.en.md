# ChatFate

[简体中文](README.md) · **English** · [繁體中文](README.zh-Hant.md)

Ask about a situation and explore a different perspective in a familiar conversation. Maintained by the ChatFate team, ChatFate is a Bazi and Liuyao plugin powered by a hosted calculation, interpretation, and report service.

[Website](https://chatfate.cc) · [Installation guide](https://chatfate.cc/install.md)

## What you can do

- **Daily guidance**: explore things to pay attention to today.
- **Ask a question**: get a Liuyao reading about a specific situation.
- **Annual themes**: explore the themes and phases of a year.
- **Life chart**: explore a personal Bazi reading.
- Sign in with Google to save and revisit your reports.

## Install or update

These instructions are for the ChatGPT / Codex desktop app on macOS, in an environment that supports Codex plugins. Pasting the instruction into an arbitrary web chat does not install the plugin.

Send this message to Codex:

> Read https://chatfate.cc/install.md and help me install the ChatFate plugin in Codex. After installation, create a new task and start using ChatFate.

Alternatively, run the website installer in Terminal:

```sh
curl -fsSL https://chatfate.cc/i | sh
```

The installer uses the Codex CLI bundled with the desktop app, installs or updates ChatFate from this repository's `main` branch, and checks that the official Browser plugin is enabled. Use the same installer to update an older installation.

After installation, select `@ChatFate` in a new task and send “Start using ChatFate.” An already-running task is not a reliable check of whether the new plugin has loaded.

If installation fails, report the error and plugin version in this repository's Issues. Never post API keys, login credentials, or private report links.

## What this repository contains

This is the public installation package: plugin declarations, the remote MCP connection configuration, Skills, and brand assets. It does not include the calculation engine, website, authentication, payments, databases, or deployment source code.

Maintainers develop and test in private, review and export only public plugin files, and publish with the ChatFate staff account. The public repository has one branch, `main`, with one release commit per published update. Private development history is not merged here.

## Please note

Bazi and Liuyao are traditional cultural interpretations, not guaranteed predictions or substitutes for medical, legal, investment, or other professional advice. ChatGPT / Codex subscriptions and model usage are separate from ChatFate report purchases.

[Privacy policy](https://chatfate.cc/privacy) · [Terms of use](https://chatfate.cc/terms)
