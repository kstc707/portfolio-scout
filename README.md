# Portfolio Scout

A skill for Claude that finds portfolio projects hiding in your own files.

Most students already have projects. They are scattered across Documents, Desktop and Downloads as a half-named notebook, a capstone PDF and a CSV. Portfolio Scout scans your computer by file name and extension, tells you honestly which pieces are worth publishing, suggests what would make each one stronger, and gets the ones you pick ready for GitHub.

## What it does

1. **Scout.** Scans every folder it can reach using file names, extensions, sizes and dates only, and ranks the folders that look like projects. It then looks inside the top-ranked ones.
2. **Shortlist.** Groups files into candidate projects and rates each one: Publish, Upgrade, Merge or Skip, with a reason and a rough effort estimate.
3. **Upgrade.** For the projects you pick, suggests specific improvements: the minimum to publish, and a few that make the project stand out.
4. **Prepare.** Builds a clean copy with a README, `.gitignore`, dependency list and licence, runs a safety check, makes the first commit, and gives you the push commands.

## What it will not do

- Open your files during the wide scan. That pass reads names only, skips system and app folders, and reports nothing except project signals, so a tax return or a medical letter never shows up in the results.
- Open folders that look like work, internship, client or private paperwork without asking you first.
- Edit, move or delete your original files. All changes happen in a new copy.
- Invent results. READMEs and resume lines describe what the code does and what a run produced.
- Publish anything without you saying yes for that project.
- Publish work, internship or client material, other people's personal data, or secrets. It flags these and suggests a public or synthetic rebuild instead.

## Install

**Claude Code.** Copy the skill folder into your skills directory:

```bash
git clone https://github.com/kstc707/portfolio-scout.git
cp -r portfolio-scout/portfolio-scout ~/.claude/skills/
```

**Claude app.** Download `portfolio-scout.zip` from this repo's Releases page and upload it where you manage skills in Claude. Anthropic's guide to packaging and uploading skills is at <https://claude.com/docs/skills/how-to>.

The skill needs access to your files, so run it somewhere Claude can read them. In a terminal it can scan your whole home folder. In apps where you connect folders one at a time, connect the broad ones (Documents, Desktop, Downloads, and wherever you keep code) and it will scan all of them. The scan uses Python 3 if it is installed.

## Use

Ask in plain words:

- "Search my computer for projects I could put on GitHub."
- "Which of my old class projects are worth turning into portfolio pieces?"
- "Get my churn analysis notebook ready for git."

Claude will run the scan, show you a shortlist, and wait for you to choose before changing anything.

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
