# did-you-check

One of the most important things in agentic development is to know what can go wrong. This is a checklist for AI coding agents, based on real bugs that came up while I was building [Slightly Parallel](https://github.com/NMar33/slightly-parallel). It is an open-source project under the MIT license: a dashboard for coding agents (Claude Code, Codex, OpenCode) that keeps all their chats, tasks and decisions in one place, and it has to work on Windows, macOS and Linux.

Every bug was written down and put into a group by its type. There are 657 of them in this list.

The file starts with a short set of questions for the agent about your project, so it can tell which parts apply to you. Then it goes to those sections, reads what broke and why, and looks for the same thing in your code:

"Did you check that …?"

## How to use it

Give your agent the link and ask it to go through your project:

```
Read https://github.com/NMar33/did-you-check/blob/main/CHECKLIST.md
and go through my project with it.
```

The bugs here will be most useful if you build harnesses and tools for your agents, or apps that have to work on different systems.

The repository will be updated once a month, and I will add bugs from my other projects.

## License

MIT
