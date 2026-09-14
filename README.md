# The .NET CLAUDE.md Starter Kit

A production-grade Claude Code setup for ASP.NET Core teams. Free, MIT, no signup.

Five files. Twenty minutes of your time. A governed AI workflow instead of five
developers improvising five different ones.

## What is in here

```
CLAUDE.md                            the project constitution, as a template
.claude/settings.json                PostToolUse hook: builds after every write
.claude/commands/security-review.md  /security-review
.claude/commands/doc-gen.md          /doc-gen
.claude/commands/pr-description.md   /pr-description
.claude/skills/api-endpoint.md       how your team adds a Minimal API endpoint
```

## Install

```bash
git clone https://github.com/[you]/dotnet-claude-starter-kit
cp -r dotnet-claude-starter-kit/.claude  /path/to/your/solution/
cp    dotnet-claude-starter-kit/CLAUDE.md /path/to/your/solution/
cd /path/to/your/solution
claude
```

Then open `CLAUDE.md` and replace every bracketed placeholder.

## Start with the hook

If you only install one thing, install `.claude/settings.json`.

It runs `dotnet build` after every file the agent writes. When the agent breaks
the build, the compiler output lands back in its context and it fixes its own
mistake before you ever see it. That is the difference between hoping the agent
produced working code and knowing it did.

Hooks load at session start, so restart Claude Code after copying the file in.

## Fill in section 4

`CLAUDE.md` has four sections. Three are obvious: overview, commands, conventions.

The fourth is **What NOT to Do**, and it is the one that changes the output most.
Every line in it should be a bug somebody on your team already shipped. A
production incident, compressed into a sentence, that the agent reads at the
start of every session. A new hire reads your onboarding doc once and forgets
half of it by week three. This file gets read every time.

The starter list is generic. Replace it with your own scar tissue.

## Contributing

Something missing? Open an issue. This is version one and it is meant to grow.

## License

MIT.
