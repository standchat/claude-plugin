# Stand Chat

[Stand Chat](https://stand.chat) puts a chat on your website. Visitors talk to you, or to your Stand-in, an AI assistant you name and brief, when you are away. This plugin lets Claude set Stand up on your website and work with your Stand account.

## Install

In a Claude Code session, version 2.1.275 or later:

```
/plugin install stand-chat --marketplace standchat/claude-plugin
```

## Use it

- **Add Stand to your website.** Where Claude can edit your website project and run commands, such as Claude Code, say "Add stand.chat". Claude installs the Stand CLI from npm, asks you to sign up or sign in in your browser, creates your Site and Stand-in, adds the chat to your website and checks it with a real chat.
- **Ask about your chats.** Connect Stand on the plugin's Connectors tab, or with `/mcp` in Claude Code, and sign in. Then ask "What questions came up most in my last ten closed conversations?" or "Add 'Ask for the order number on returns' to my Site instructions. Show me the change before saving it."

If you already added Stand as a custom connector, remove it so Claude does not see each tool twice.

## Data

- The connector reaches `https://app.stand.chat/mcp` as the Stand user who signs in. Stand's sign-in page lists its permissions, and you approve all of them or none. With them, Claude reads your profile, plan, usage and team, reads and changes your Sites, Stand-ins, knowledge and Site instructions, and reads statistics and conversations, including what visitors wrote.
- The setup skill installs and runs the Stand CLI (`@standchat/cli`). The CLI keeps its sign-in in your system's credential store and sends Stand what you approve: Site details, Stand-in instructions, and the public pages or documents to learn from.
- The plugin stores nothing itself and runs no hooks or local programs.

Stand's [privacy policy](https://stand.chat/privacy) covers the data in your account. For help, write to crew@stand.chat.

## Guides

- [Use Stand from your AI chat (MCP)](https://stand.chat/guide/mcp)
- [Stand CLI](https://stand.chat/guide/cli)
- [Add stand.chat with a coding agent](https://stand.chat/guide/ai-setup)
