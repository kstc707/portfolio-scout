---
name: portfolio-scout
description: Scan a folder for coursework, notebooks, scripts and dashboards that could become portfolio projects, suggest upgrades, and get the chosen ones GitHub-ready. Use for "find projects in my files".
---

# Portfolio Scout

Most students and early-career people already have portfolio projects. They are sitting in a Downloads or coursework folder as half-named notebooks, a capstone PDF, and a CSV. This skill finds that work, says honestly which pieces are worth publishing, suggests what would make each one stronger, and prepares the chosen ones for GitHub.

Use it when someone asks to find projects in their files, turn old coursework into a portfolio, clean up a project for GitHub, or work out what they could show an employer.

The work runs in four stages. Stop after stage 2 and let the person choose before changing anything.

1. Scout: inventory one folder, read-only
2. Shortlist: group files into candidate projects and rate them
3. Upgrade: suggest specific changes for the ones the person picks
4. Prepare: build a clean, git-ready copy and hand over the push commands

## Ground rules

These exist because the person is trusting you with their whole folder, and because a portfolio that overstates the work fails the moment an interviewer asks about it.

- **One folder, chosen by the person.** Ask which folder to scan. Do not scan a home directory or a whole drive. If they are unsure, suggest the folder where they keep coursework or side projects.
- **Read-only until they pick.** Stages 1 and 2 only list and read files.
- **Never edit, move or delete originals.** All preparation happens in a new copy (see stage 4). The person's original files stay exactly as they were.
- **Describe only what exists.** A README, a metric, or a resume line states what the code in the repo does and what a run of it produced. Ideas that are not built yet go under a "Planned" heading. Do not invent accuracy numbers, user counts, or results. If a notebook's saved output shows 0.91 accuracy, you can cite it; if you cannot find or reproduce a number, leave it out and say so.
- **Nothing goes public without a yes.** Creating a public repo, or pushing to one, needs the person's explicit go-ahead for that specific project.

## Stage 1: Scout

Get a picture of the folder before reading anything in depth. List files to a depth of about four levels, skipping noise:

```bash
find "$FOLDER" -maxdepth 4 -type f \
  -not -path '*/.git/*' -not -path '*/node_modules/*' \
  -not -path '*/__pycache__/*' -not -path '*/.venv/*' -not -path '*/venv/*' \
  -not -name '.DS_Store' \
  -printf '%s\t%TY-%Tm-%Td\t%p\n' | sort -k3
```

On macOS, `find` has no `-printf`; use `-exec stat -f '%z%t%Sm%t%N' -t '%Y-%m-%d' {} +` instead. If there is no shell, use whatever directory-listing tool is available.

Files that signal real work:

| Signal | Examples |
| --- | --- |
| Analysis and modelling | `.ipynb`, `.py`, `.R`, `.Rmd`, `.sql`, `.m` |
| Dashboards and BI | `.pbix`, `.twbx`, `.xlsx` with pivot tables or charts |
| Apps and tools | `package.json`, `app.py`, `main.*`, `Dockerfile`, `index.html` |
| Write-ups | report or capstone PDFs, `.docx`, `.pptx`, `.tex`, posters |
| Data | `.csv`, `.parquet`, `.json`, `.db` |
| Already a repo | a `.git` directory (check `git remote -v` and `git status`) |

Then read enough of each cluster to know what it is: the first and last cells of a notebook, the headings of a report, the imports and main function of a script. Do not read every file in full. Large data files only need their header row and row count.

## Stage 2: Shortlist

Group related files into candidate projects. A notebook, the CSV it loads, and the PDF report written from it are one project even if they sit in different subfolders. Files that share a dataset, a course code, or a date range usually belong together.

Rate each candidate on five questions:

1. **Is it finished enough?** Does it reach a result, or stop halfway?
2. **Is it theirs?** A tutorial followed step by step, or a solution to a set assignment, is weaker than a self-directed project. It can still be a starting point if they extend it.
3. **Can the data be shared?** Public dataset, synthetic, or something private that has to be swapped out?
4. **Does it match where they are headed?** Ask what roles they are aiming for if you do not know. A supply-chain dashboard matters more for an analyst role than for a backend one.
5. **How much work to make it presentable?** An hour, an afternoon, or a week?

Give each a verdict: **Publish** (needs only cleanup), **Upgrade** (worth it after specific changes), **Merge** (too small alone, combine with another), or **Skip** (say why in one line).

Report the shortlist in this shape, best candidates first:

```
## Candidate projects in <folder>

| # | Project | What it is | Verdict | Effort |
|---|---------|-----------|---------|--------|
| 1 | Churn analysis | Notebook + 7k-row public dataset, logistic regression, report PDF | Publish | ~2 hrs |
| 2 | Sales dashboard | Power BI file, no data source documented | Upgrade | ~1 day |
| 3 | SQL homework set | 12 assignment answers | Skip: set coursework | - |

### 1. Churn analysis
- Files: `ml-class/final/churn.ipynb`, `ml-class/final/telco.csv`, `ml-class/final/report.pdf`
- What is there: cleaning, 3 models compared, ROC curve; saved output shows AUC 0.84
- What is missing: no README, hard-coded file path, no requirements file
- Check before publishing: confirm the dataset licence
```

Then ask which ones to take forward. Do not start stage 3 on everything at once; one well-finished project beats five rushed ones.

## Stage 3: Upgrade

For each chosen project, suggest changes in two tiers and let the person decide how far to go.

**Tier 1, needed to publish (hours).** Make it run from a clean start, remove hard-coded paths, add a README, list dependencies, clear or check notebook outputs, name files sensibly.

**Tier 2, makes it stand out (days).** Pick the two or three that fit the project; do not suggest all of them.

| Project type | Upgrades that add real substance |
| --- | --- |
| ML model | A baseline to compare against; cross-validation instead of one split; error analysis on what the model gets wrong; a short "limitations" section |
| Data analysis | A clear question at the top and an answer at the bottom; a data-quality section (nulls, duplicates, outliers and what was done); one chart per finding |
| Dashboard | Screenshots or a short GIF in the README; a data dictionary; the questions the dashboard answers, written out |
| Pipeline / ETL | A diagram of the flow; data validation checks; a small sample input and expected output; a single command to run it |
| App or tool | A deployed link or demo recording; a few tests; setup steps that work on a fresh machine |
| Report only, code lost | Rebuild the core analysis from the report, and say in the README that it is a rebuild |

Say which upgrades you can do now and which need the person (re-running a long training job, exporting from Power BI, recording a demo). Keep the person's own approach. The goal is their project, cleaner, not a different project with their name on it.

If nothing in the folder fits the roles they want, say so plainly and suggest one or two project ideas that build on skills the folder does show. Label them as ideas to build, not as existing work.

## Stage 4: Prepare for git

### 4a. Safety check first

Run this before copying anything into a repo. Publishing is hard to undo: a secret pushed to GitHub should be treated as leaked even if the commit is deleted later.

**Secrets.** Search the project files:

```bash
grep -rIniE --exclude-dir=.git --exclude-dir=node_modules \
  -e '(api[_-]?key|secret|token|passw(or)?d)[a-z_]*[[:space:]]*[:=]' \
  -e 'AKIA[0-9A-Z]{16}' -e 'sk-[A-Za-z0-9_-]{20,}' -e 'ghp_[A-Za-z0-9]{36}' \
  -e '-----BEGIN [A-Z ]*PRIVATE KEY-----' \
  -e '(postgres|mysql|mongodb(\+srv)?)://[^ ]+:[^ ]+@' "$PROJECT"
```

Also look for `.env` files, `credentials.json`, and keys pasted into notebook cells. Move real values to environment variables, commit a `.env.example` with placeholders, and tell the person to rotate any key that was ever in a file they shared.

**Other people's information.** Datasets with real names, emails, phone numbers, student or patient IDs, or addresses do not get published. Replace with a public dataset or generate synthetic data with the same shape, and say so in the README.

**Work, internship and client material.** Anything that came from a job, an internship, a client, a research lab, or a government office stays out unless the person has written permission to publish it. That includes data, code, screenshots, and internal figures. The fix is to rebuild the same technique on public or synthetic data and describe it as that.

**Coursework rules.** Posting solutions to set assignments can break a course's academic integrity policy, and some instructors ask that solutions stay private. Flag this and let the person check their course policy. Self-directed projects and capstones are usually fine. Group projects need teammates' agreement and credit in the README.

**Dataset licence.** If the licence allows redistribution, include the data or a sample. If it is unclear, link to the source and add a short download step instead of committing the file.

**Size.** GitHub warns above 50 MB per file and rejects files above 100 MB. Keep large data and model files out of the repo; commit a small sample and document where the full file comes from.

**Notebook outputs.** Saved outputs can contain rows of the data and local file paths with a username in them. Read through them, and clear outputs that show anything from the lists above.

**Existing history.** If the folder is already a git repo and a secret or private file appears anywhere in its history, start a fresh history in the new copy instead of pushing the old one.

Report what you found and what you did about each item. If something cannot be made safe, say the project should stay private.

### 4b. Build a clean copy

Create the repo as a new folder next to the originals, for example `<scanned folder>/_portfolio/<project-slug>/`, and copy in only what belongs. Use a short lowercase hyphenated name that says what the project does (`customer-churn-analysis`, not `final_project_v3`).

A layout that suits most data projects; drop the folders a project does not need:

```
project-slug/
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt        # or environment.yml, renv.lock, package.json
├── data/
│   ├── README.md           # where the data comes from and how to get it
│   └── sample.csv          # small sample, only if the licence allows
├── notebooks/              # numbered in run order: 01_cleaning.ipynb ...
├── src/                    # functions pulled out of notebooks
└── reports/
    └── figures/            # charts used in the README
```

Then:

- **Fix paths.** Replace absolute paths (`C:\Users\...`, `/Users/name/...`) with paths relative to the repo.
- **List dependencies from what the code imports**, not from everything installed on the machine. Pin versions only when you know the versions the project ran with.
- **Run it.** Where the environment allows, run the project from a clean start and confirm it finishes. If you could not run it (missing data, licensed software, long training time), say so in your summary to the person. Do not write "tested" or "reproducible" in the README unless it was.
- **Add a `.gitignore`** suited to the language: virtual environments, `__pycache__/`, `.ipynb_checkpoints/`, `.env`, `.DS_Store`, `.Rhistory`, `node_modules/`, and the large data files.
- **Add a licence** if the person wants others to reuse the code. MIT is a common default for portfolio code; ask rather than assume, and use their name as the copyright holder.

### 4c. Write the README

The README is what a recruiter or interviewer reads, usually for under a minute. Lead with what the project found or does, not with setup steps.

```markdown
# Project title

One or two sentences: the question or problem, and the result.

![Key chart or screenshot](reports/figures/main.png)

## What this project does
- The problem and why it matters
- The data: source, size, time period
- The approach: main steps and methods
- The result: findings or metrics, with numbers taken from the actual run

## How to run it
1. Install: `pip install -r requirements.txt`
2. Get the data: <link or command>
3. Run: `jupyter notebook notebooks/01_cleaning.ipynb`

## Project structure
Brief map of the folders.

## Limitations and next steps
What the analysis does not cover, and what is planned but not built.

## Credits
Dataset source and licence, course or tutorial this started from, teammates.
```

Write it in the person's voice and at their level. If the project began as coursework or a tutorial, the Credits section says so; that is normal and reads better than pretending otherwise.

### 4d. Commit and hand over

```bash
cd project-slug
git init -b main
git add .
git status            # read the list before committing; nothing private should appear
git commit -m "Initial commit: <what the project does>"
```

Use real commits with real dates. Do not backdate commits or split work into artificial history to make a project look older or longer-running than it is.

To publish, give the person the commands and let them run the push, or run it yourself only if you have access and they have said yes for this project:

```bash
# with the GitHub CLI
gh repo create project-slug --private --source=. --push
# or, after creating an empty repo on github.com
git remote add origin https://github.com/<username>/project-slug.git
git push -u origin main
```

Suggest starting **private**. They can look it over on GitHub, then switch it to public in the repo settings when they are happy. After it is public, suggest adding a one-line description and topics on the repo page, and pinning it on their profile.

## Finish

Tell the person, briefly:

- which projects were prepared and where the new folders are
- what you changed and what you could not verify
- anything they must do themselves (rotate a key, check a course policy, export a dashboard screenshot, run the push)
- what is still a "Planned" item

If they want it for a resume, offer one or two lines per project that describe what the repo actually contains, with only the numbers that appear in it.
