# did-you-check

One of the most important things in agentic development is to know what can go wrong. This is a checklist for AI coding agents, built from 657 real bugs that came up while I was building [Slightly Parallel](https://github.com/NMar33/slightly-parallel), an open-source dashboard for coding agents. Every bug was written down and grouped by what actually went wrong, and each group became a question you can check in your own code: "Did you check that …?"

## How to use it

Give your agent the link and ask it to go through your project:

```
Read https://github.com/NMar33/did-you-check/blob/main/CHECKLIST.md
and go through my project with it.
```

The agent first answers a few questions about your project, so it only looks at the parts that apply to you. Then it checks the code for each question and answers yes, no or unverified, with evidence. At the end it lists what it found, starting with the most serious problems. It does not change your code.

## What is inside

- 58 questions in six sections: the product, data and results, the environment, external systems, the user, tests and release.
- For each question: when it applies, what breaks, a real case, how to check it, and how it is usually fixed.
- A short list of things to check before you say the work is done.

It will be most useful if you build harnesses and tools for your agents, or apps that have to work on different systems.

The list will be updated once a month, and I will add bugs from my other projects.

## License

MIT
