# Portfolio Scout

A skill for Claude that finds portfolio projects hiding in your own files.

Most students already have projects. They are sitting in a coursework folder as a half-named notebook, a capstone PDF and a CSV. Portfolio Scout scans one folder you choose, tells you honestly which pieces are worth publishing, suggests what would make each one stronger, and gets the ones you pick ready for GitHub.

## What it does

1. **Scout.** Lists the files in one folder you choose. Read-only.
2. **Shortlist.** Groups files into candidate projects and rates each one: Publish, Upgrade, Merge or Skip, with a reason and a rough effort estimate.
3. **Upgrade.** For the projects you pick, suggests specific improvements: the minimum to publish, and a few that make the project stand out.
4. **Prepare.** Builds a clean copy with a README, `.gitignore`, dependency list and licence, runs a safety check, makes the first commit, and gives you the push commands.

## What it will not do

- Edit, move or delete your original files. All changes happen in a new copy.
- Invent results. READMEs and resume lines describe what the code does and what a run produced.
- Publish anything without you saying yes for that project.
- Publish work, internship or client material, other people's personal data, or secrets. It flags these and suggests a public or synthetic rebuild instead.

## Install

**Claude Code.** Copy the skill folder into your skills directory:

```bash
git clone https://github.com/<your-username>/portfolio-scout.git
cp -r portfolio-scout/portfolio-scout ~/.claude/skills/
```

**Claude app.** Download `portfolio-scout.zip` from this repo's Releases page and upload it where you manage skills in Claude. Anthropic's guide to packaging and uploading skills is at <https://claude.com/docs/skills/how-to>.

The skill needs access to your files to be useful, so run it somewhere Claude can read a folder on your computer.

## Use

Ask in plain words:

- "Find projects in my coursework folder that I could put on GitHub."
- "Which of my old class projects are worth turning into portfolio pieces?"
- "Get my churn analysis notebook ready for git."

Claude will ask which folder to scan, show you a shortlist, and wait for you to choose before changing anything.

## Before you publish a project

The skill checks these, but they are your call in the end:

- **Course policy.** Some courses do not allow posting assignment solutions. Capstones and self-directed projects are usually fine.
- **Work and internship data.** If it came from an employer, a client or a lab, it stays private unless you have written permission.
- **Group projects.** Ask your teammates and credit them.
- **Dataset licence.** Link to the source if you are not sure you can redistribute it.

## Contributing

Suggestions are welcome, especially from students in fields other than data (design, hardware, writing, research). Open an issue describing the kind of project the skill missed or handled badly.

## Licence

MIT. See [LICENSE](LICENSE).
