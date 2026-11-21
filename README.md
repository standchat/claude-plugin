# Stand Chat for Claude

The Stand Chat plugin for Claude is in [stand-chat/](stand-chat/). It adds Stand to your website and connects Claude to your Stand account.

## Install in the Claude desktop app

1. Open **Plugins** in the sidebar and choose **Add → Add marketplace**.
2. Enter `standchat/claude-plugin`.
3. Select **Add to this computer** next to Stand Chat.
4. On the plugin's page, select **Connect** next to `stand` and sign in to Stand.

## Install in Claude Code

In a terminal:

```
claude plugin marketplace add standchat/claude-plugin
```

```
claude plugin install stand-chat@standchat
```

Then say "Add stand.chat" in your website project, or run `/mcp` and sign in to Stand to ask about your chats.

## Claude on the web

Connect Stand as a custom connector by following [Use Stand from your AI chat](https://stand.chat/guide/mcp).

## About this repository

This repository is published from Stand's main repository, and changes made here are replaced at the next release. For help, write to crew@stand.chat.
