# did-you-check

This is a checklist for AI coding agents, based on real bugs. While building an open-source app with coding agents, I kept a record of every bug we found: 657 of them over three months. I grouped them by what actually went wrong, not by how they looked, and turned each group into a question you can check in your own code: "Did you check that …?"

## How to use it

Give your agent the link and ask it to go through your project:

```
Read https://github.com/NMar33/did-you-check/blob/main/CHECKLIST.md
and go through my project with it.
```

The agent answers a few questions about your project first, so it only looks at the parts that apply to you. Then, for each question, it checks the code and gives an answer with evidence. At the end it lists what it found, starting with the most serious problems.

## What is inside

- 58 questions in six parts: the product, data and results, the environment, external systems, the user, tests and release.
- For each question: when it applies, what breaks, a real case, how to check it, and how it is usually fixed.
- A short list of things to check before you say the work is done.

Most of the bugs come from one project so far. More will be added from other projects over time.

## License

[CC BY 4.0](LICENSE). You are free to use it and change it, as long as you link back here.
