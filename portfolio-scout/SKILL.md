---
name: portfolio-scout
description: Scan a computer by file names and extensions to find coursework, notebooks and apps that could become portfolio projects, suggest upgrades, and get the chosen ones GitHub-ready.
---

# Portfolio Scout

Most students and early-career people already have portfolio projects. They are scattered across Documents, Desktop and Downloads as half-named notebooks, a capstone PDF, and a CSV. This skill finds that work wherever it sits, says honestly which pieces are worth publishing, suggests what would make each one stronger, and prepares the chosen ones for GitHub.

Use it when someone asks to find projects in their files, turn old coursework into a portfolio, clean up a project for GitHub, or work out what they could show an employer.

The work runs in four stages. Stop after stage 2 and let the person choose before changing anything.

1. Scout: scan everything you can reach by file name and extension, then look inside the folders that rank highest
2. Shortlist: group files into candidate projects and rate them
3. Upgrade: suggest specific changes for the ones the person picks
4. Prepare: build a clean, git-ready copy and hand over the push commands

## Ground rules

These exist because the person is trusting you with their whole computer, and because a portfolio that overstates the work fails the moment an interviewer asks about it.

- **The wide scan reads names, not contents.** The first pass uses file names, extensions, sizes and dates only. It does not open a single file. Say this in one line before you start; running the skill is the request, so do not ask for permission again.
- **Report project signals only.** A whole-computer listing includes tax returns and medical letters. Never print or summarise file names that are not project signals. The scan script already filters them out; do not add them back with your own listing commands.
- **Open files only inside candidate folders.** Folders the scan marks FLAGGED (paths that suggest a job, internship, client, or private paperwork) are named in the results but not opened until the person says so.
- **Read-only until they pick.** Stages 1 and 2 only list and read.
- **Never edit, move or delete originals.** All preparation happens in a new copy (see stage 4). The person's original files stay exactly as they were.
- **Describe only what exists.** A README, a metric, or a resume line states what the code in the repo does and what a run of it produced. Ideas that are not built yet go under a "Planned" heading. Do not invent accuracy numbers, user counts, or results. If a notebook's saved output shows 0.91 accuracy, you can cite it; if you cannot find or reproduce a number, leave it out and say so.
- **Nothing goes public without a yes.** Creating a public repo, or pushing to one, needs the person's explicit go-ahead for that specific project.

## Stage 1: Scout

### 1a. Work out what you can reach

"The whole computer" means the person's own files. System and application folders never hold their projects, and the scan skips them.

- **Full file access** (a terminal agent, or a shell on their machine): scan the home folder. Add other drives or volumes if they keep work there (`D:\`, an external disk).
- **Per-folder access** (apps where the person connects folders one at a time): you can only scan what is connected, and a whole home folder often cannot be connected. Ask them to connect the broad ones in one go, usually Documents, Desktop, Downloads, and wherever they keep code, then scan all of them together. Tell them which places were not covered.

Cloud-synced folders (OneDrive, iCloud, Google Drive) are fine to scan: listing names does not download anything.

### 1b. Run the names-only scan

Save the script from the appendix at the end of this file to a temporary location outside the person's folders, then run it:

```bash
python3 scan.py                               # home folder
python3 scan.py ~/Documents ~/Desktop ~/code   # specific places
python3 scan.py --top 80 --json               # more results, machine-readable
```

On Windows the command may be `python` or `py`, and a second drive is passed the same way (`py scan.py D:\`). A home folder usually takes a few seconds to a minute.

The script walks every folder it is allowed to, skips installed software and caches, groups files into project folders, and prints a ranked list:

```
 11  2025-04-30  git ~/code/shop-app  [code=42 data=1]
       package.json, index.ts, page.tsx
 10  2024-12-09  ~/Documents/school/ML/final-project  [code=2 data=1 docs=1]
       churn.ipynb, eda.ipynb
  7  2025-03-02  ~/Documents/school/BI  [bi=1 data=1]
       Supply Chain Dashboard.pbix
  4  2025-08-14  FLAGGED ~/Documents/internship/audit  [code=1 data=1]
```

Each line is score, last-modified date, folder, and counts by kind. `git` means the folder is already a repository. FLAGGED folders show no file names.

What it counts:

| Kind | Extensions |
| --- | --- |
| code | `.ipynb .py .R .Rmd .qmd .sql .m .js .ts .java .cpp .cs .go .rs .html` and similar |
| bi | `.pbix .pbip .twb .twbx` |
| design | `.fig .psd .ai .sketch .xd .indd .blend .dwg` |
| data | `.csv .tsv .parquet .xlsx .db .sqlite .dta .sav` |
| docs | `.pdf .docx .pptx .tex`, counted only beside code, or when the name says capstone, thesis, project or poster |
| markers | `requirements.txt`, `package.json`, `pyproject.toml`, `.Rproj`, `Dockerfile`, `README` |

If Python is not available, do the same thing by hand: list files recursively with the tool you have, keep only the extensions above, skip the folders in the script's `SKIP_DIRS`, and count per folder.

### 1c. Judge the list

The score is a rough sort, not a verdict. Before looking inside anything, drop what is plainly not the person's work:

- tool installs, SDK samples and course starter kits (many files, generic names, old identical dates)
- downloaded copies of someone else's repository
- folders of exported or auto-generated files

Tell the person in two or three lines what the scan covered and how many candidates it found.

### 1d. Look inside the best candidates

Open the top candidates, about ten, without asking again. For FLAGGED folders, list them and ask first.

Read enough of each to know what it is: the first and last cells of a notebook, the headings of a report, the imports and main function of a script. Do not read every file in full. Large data files only need their header row and row count.

For folders that are already git repositories, check whose work it is before anything else:

```bash
git -C "$DIR" remote -v                       # someone else's repo, or already on their GitHub?
git -C "$DIR" log --format='%an' | sort | uniq -c   # who wrote the commits
git -C "$DIR" status --short | head           # unpushed local work
```

A clone of another person's project is not a portfolio piece. A repo of their own that is already public may only need a better README.

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
## Candidate projects found

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

If nothing the scan found fits the roles they want, say so plainly and suggest one or two project ideas that build on skills their files do show. Label them as ideas to build, not as existing work.

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

If the project is already a git repository, search its history too, because a key deleted from the current files is still in old commits:

```bash
git -C "$PROJECT" log -p --all | grep -nIiE '(api[_-]?key|secret|token|passw(or)?d)[a-z_]*[[:space:]]*[:=]|AKIA[0-9A-Z]{16}|ghp_[A-Za-z0-9]{36}|BEGIN [A-Z ]*PRIVATE KEY' | head -50
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

Create the repo as a new folder, for example `~/portfolio/<project-slug>/` or a place the person names, and copy in only what belongs. Use a short lowercase hyphenated name that says what the project does (`customer-churn-analysis`, not `final_project_v3`).

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

## Appendix: scan.py

Save this as `scan.py` and run it as described in stage 1b. It uses only the Python standard library and never opens a file.

```python
#!/usr/bin/env python3
"""Portfolio Scout wide scan.

Walks the given folders (default: the home folder) and ranks folders that look
like projects, using file names, extensions, sizes and dates only.
It never opens a file. Standard library only; runs on macOS, Windows, Linux.

Usage: python3 scan.py [folder ...] [--top N] [--json]
"""
import json
import os
import sys
import time

CODE = {".ipynb", ".py", ".r", ".rmd", ".qmd", ".sql", ".m", ".jl", ".do", ".sas",
        ".js", ".ts", ".jsx", ".tsx", ".java", ".c", ".cpp", ".h", ".cs", ".go",
        ".rs", ".php", ".rb", ".swift", ".kt", ".dart", ".html", ".css", ".sh",
        ".ino", ".v", ".vhd"}
BI = {".pbix", ".pbip", ".twb", ".twbx", ".qvf"}
DESIGN = {".fig", ".psd", ".ai", ".sketch", ".xd", ".indd", ".blend", ".f3d",
          ".sldprt", ".dwg"}
DATA = {".csv", ".tsv", ".parquet", ".feather", ".xlsx", ".xls", ".db",
        ".sqlite", ".sav", ".dta", ".rds"}
DOCS = {".pdf", ".docx", ".pptx", ".tex", ".key"}

MARKER_FILES = {"requirements.txt", "pyproject.toml", "environment.yml", "setup.py",
                "package.json", "dockerfile", "cargo.toml", "pom.xml", "build.gradle",
                "makefile", "renv.lock", "go.mod", "pubspec.yaml", "readme",
                "readme.md", "readme.txt", "description"}
MARKER_EXTS = {".rproj", ".sln", ".csproj", ".pbip"}
WEAK_MARKERS = {"readme", "readme.md", "readme.txt", "description", "makefile"}

# Folders that never hold a person's own projects, or are installed software.
SKIP_DIRS = {
    "node_modules", "site-packages", "__pycache__", "venv", "env", "virtualenvs",
    "dist", "build", "target", "vendor", "bower_components", "pods", "deriveddata",
    "anaconda3", "miniconda3", "miniforge3", "mambaforge", "conda", "pkgs",
    "library", "applications", "system", "private", "cores", "volumes",
    "appdata", "application data", "program files", "program files (x86)",
    "programdata", "windows", "$recycle.bin", "system volume information",
    "proc", "sys", "dev", "usr", "nix", "bin", "sbin", "etc", "var", "boot", "snap",
    "lib", "lib64", "opt", "run", "tmp", "lost+found",
    "music", "movies", "pictures", "photos", "videos", "games", "steam",
}
SKIP_SUFFIXES = (".app", ".framework", ".bundle", ".photoslibrary", ".lproj",
                 ".xcodeproj", ".egg-info", "_files")
# Sub-folders that belong to the project above them.
ROLL_UP = {"data", "datasets", "raw", "processed", "src", "source", "code",
           "scripts", "notebooks", "notebook", "reports", "report", "figures",
           "figs", "plots", "images", "img", "assets", "output", "outputs",
           "results", "models", "tests", "test", "docs", "doc", "sql", "app",
           "analysis", "templates", "static", "public", "components", "pages",
           "utils", "include", "resources"}

NAME_HINTS = ("capstone", "thesis", "dissertation", "project", "final", "hackathon",
              "portfolio", "dashboard", "analysis", "predict", "model", "research",
              "assignment", "homework", "poster", "prototype")
DOC_ONLY_HINTS = ("capstone", "thesis", "dissertation", "project", "poster")
# Paths that may hold work or private material: listed, but never opened unasked.
# Matched against whole words in folder names, so "coursework" is not "work".
FLAG_WORDS = {"work", "job", "jobs", "intern", "internship", "internships", "client",
              "clients", "employer", "company", "office", "confidential", "nda",
              "tax", "taxes", "medical", "health", "bank", "banking", "payroll",
              "legal", "insurance", "invoices"}


def parse_args(argv):
    roots, top, as_json = [], 40, False
    it = iter(argv)
    for a in it:
        if a == "--top":
            top = int(next(it))
        elif a == "--json":
            as_json = True
        else:
            roots.append(a)
    return roots or [os.path.expanduser("~")], top, as_json


def new_entry():
    return {"code": 0, "bi": 0, "design": 0, "data": 0, "docs": 0, "markers": set(),
            "git": False, "latest": 0.0, "bytes": 0, "examples": [], "doc_hits": []}


def scan(roots):
    found, walked, denied = {}, 0, 0

    def on_error(_err):
        nonlocal denied
        denied += 1

    for root in roots:
        root = os.path.abspath(os.path.expanduser(root))
        for path, dirs, files in os.walk(root, topdown=True, onerror=on_error):
            walked += 1
            has_git = ".git" in dirs
            dirs[:] = [d for d in dirs
                       if not d.startswith((".", "$", "~"))
                       and d.lower() not in SKIP_DIRS
                       and not d.lower().endswith(SKIP_SUFFIXES)]
            e = None
            for name in files:
                if name.startswith((".", "~$")):
                    continue
                low = name.lower()
                ext = os.path.splitext(low)[1]
                kind = ("code" if ext in CODE else "bi" if ext in BI else
                        "design" if ext in DESIGN else "data" if ext in DATA else
                        "docs" if ext in DOCS else None)
                marker = low in MARKER_FILES or ext in MARKER_EXTS
                if not kind and not marker:
                    continue
                if e is None:
                    e = new_entry()
                if marker:
                    e["markers"].add(name)
                if not kind:
                    continue
                e[kind] += 1
                try:
                    st = os.stat(os.path.join(path, name))
                    e["latest"] = max(e["latest"], st.st_mtime)
                    e["bytes"] += st.st_size
                except OSError:
                    pass
                if kind == "docs":
                    if any(h in low for h in DOC_ONLY_HINTS):
                        e["doc_hits"].append(name)
                elif kind != "data" and len(e["examples"]) < 5:
                    e["examples"].append(name)
            if has_git:
                e = e or new_entry()
                e["git"] = True
            if e:
                found[path] = e
    return found, walked, denied


def strong(e):
    return bool(e) and any(m.lower() not in WEAK_MARKERS for m in e["markers"])


def project_root(path, found, roots):
    """The folder that owns this one: the nearest enclosing git repo, else the
    folder itself if it is a marked project, else a marked project up to three
    levels above, else the parent of conventional sub-folders (data/, src/)."""
    chain, cur = [], path
    while cur not in roots:          # a scan root never owns everything under it
        chain.append(cur)
        parent = os.path.dirname(cur)
        if parent == cur:
            break
        cur = parent
    for c in chain:
        if found.get(c, {}).get("git"):
            return c
    if not chain or strong(found.get(path)):
        return path
    for c in chain[1:4]:
        if strong(found.get(c)):
            return c
    i = 0
    while i + 1 < len(chain) and os.path.basename(chain[i]).lower() in ROLL_UP:
        i += 1
    return chain[i]


def merge(found, roots):
    roots = set(roots)
    out = {}
    for path, e in found.items():
        root = project_root(path, found, roots)
        t = out.setdefault(root, new_entry())
        for k in ("code", "bi", "design", "data", "docs", "bytes"):
            t[k] += e[k]
        t["latest"] = max(t["latest"], e["latest"])
        t["doc_hits"] += e["doc_hits"]
        t["examples"] = (t["examples"] + e["examples"])[:5]
        if path == root:
            t["markers"] |= e["markers"]
            t["git"] = t["git"] or e["git"]
    return out


def score(path, e):
    made = e["code"] + e["bi"] + e["design"]
    names = " ".join([os.path.basename(path)] + e["examples"]).lower()
    if made == 0:
        # Loose documents only count when their name says project work.
        if not (e["doc_hits"] or e["git"]):
            return 0
        return 2 * min(len(e["doc_hits"]), 3) + (2 if e["git"] else 0)
    s = min(e["code"], 10) * 3 + e["bi"] * 4 + min(e["design"], 5) * 2
    s += min(e["data"], 5) + min(e["docs"], 3)
    s += 3 if e["markers"] else 0
    s += 2 if e["git"] else 0
    s += 2 if any(h in names for h in NAME_HINTS) else 0
    return s


def is_flagged(path, roots):
    rel = path
    for r in roots:
        if path.startswith(r):
            rel = path[len(r):]
            break
    words = "".join(c if c.isalnum() else " " for c in rel.lower()).split()
    return any(w in FLAG_WORDS for w in words)


def main():
    roots, top, as_json = parse_args(sys.argv[1:])
    roots = [os.path.abspath(os.path.expanduser(r)) for r in roots]
    started = time.time()
    found, walked, denied = scan(roots)
    rows = []
    for path, e in merge(found, roots).items():
        s = score(path, e)
        if s <= 0:
            continue
        flagged = is_flagged(path, roots)
        rows.append({
            "score": s, "path": path, "flagged": flagged,
            "code": e["code"], "bi": e["bi"], "design": e["design"],
            "data": e["data"], "docs": e["docs"], "git": e["git"],
            "markers": sorted(e["markers"])[:4],
            "last_modified": time.strftime("%Y-%m-%d", time.localtime(e["latest"]))
                             if e["latest"] else "",
            "size_mb": round(e["bytes"] / 1e6, 1),
            # File names are withheld for flagged folders.
            "examples": [] if flagged else (e["examples"] + e["doc_hits"])[:5],
        })
    rows.sort(key=lambda r: (-r["score"], r["path"]))
    summary = {"roots": roots, "folders_walked": walked, "unreadable": denied,
               "candidates": len(rows), "seconds": round(time.time() - started, 1)}
    if as_json:
        print(json.dumps({"summary": summary, "candidates": rows[:top]}, indent=1))
        return
    print("Scanned {roots}: {folders_walked} folders in {seconds}s, "
          "{candidates} candidates, {unreadable} unreadable".format(**summary))
    for r in rows[:top]:
        kinds = " ".join("%s=%d" % (k, r[k])
                         for k in ("code", "bi", "design", "data", "docs") if r[k])
        tags = ("git " if r["git"] else "") + ("FLAGGED " if r["flagged"] else "")
        print("%3d  %s  %s%s  [%s]" % (r["score"], r["last_modified"], tags,
                                       r["path"], kinds))
        if r["markers"] or r["examples"]:
            print("       " + ", ".join(r["markers"] + r["examples"]))
    if len(rows) > top:
        print("... %d more; rerun with --top %d" % (len(rows) - top, len(rows)))


if __name__ == "__main__":
    try:
        main()
    except BrokenPipeError:
        pass
```
