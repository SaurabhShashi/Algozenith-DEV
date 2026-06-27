# Git & GitHub — Version Control Mastery (Lectures 3 & 4)

> Complete guide from "what is version control" through branching, merging, rebasing, and open-source collaboration on GitHub. Written for interview preparation and daily engineering work.

---

## 0. Why Does Git Exist? (The Problems It Solves)

Before Git, teams faced these nightmares daily:

| Problem | What Goes Wrong | How Git Fixes It |
|---------|----------------|------------------|
| **Overwriting work** | Person A edits lines 5–10; Person B edits lines 7–20 in the same file → Person B accidentally erases A's work | Git tracks *who* changed *what*, *when* — and alerts you to conflicts |
| **Merging chaos** | 10 people work on the same file → combining their changes is a manual nightmare | Git merges automatically where possible; asks for help only on true conflicts |
| **No history** | "Who deleted that function? When? Why?" — impossible to answer without tracking | Every change is a permanent snapshot with author, timestamp, and message |
| **Fear of breaking things** | You want to try a risky feature but are terrified of breaking the live product | Branches let you experiment in isolation; merge only when safe |
| **Collaboration at scale** | Thousands of contributors (open-source) can't all have direct write access | Fork → branch → PR workflow lets anyone propose changes safely |

> **Interview framing:** "Why do teams use version control?" is a culture-fit question. The strong answer hits **collaboration, history, safety (branching), and accountability** — not just "to save code."

---

## 1. Types of Version Control Systems

### Centralized (CVCS) — e.g., SVN, CVS

```
         ┌──────────────────┐
         │  CENTRAL SERVER   │  ← single source of truth
         │  (all history)    │
         └────────┬─────────┘
          ┌───────┼───────┐
          │       │       │
       Dev A   Dev B   Dev C     ← only have their working files
```

**Fatal flaw:** server goes down → nobody can commit, view history, or collaborate. Requires constant network connection.

### Distributed (DVCS) — e.g., Git, Mercurial

```
         ┌──────────────────┐
         │   REMOTE REPO    │  ← GitHub/GitLab (convenience, not dependency)
         │  (full history)   │
         └────────┬─────────┘
          ┌───────┼───────┐
          │       │       │
       Dev A   Dev B   Dev C
       (full   (full   (full     ← EVERY dev has the COMPLETE repo + history
       history) history) history)
```

**Key insight:** even if the remote server explodes, every developer has the *entire* project history locally. You can commit, branch, view history — all offline. The remote is for sharing, not dependency.

> **Interview question:** "What's the difference between Git and GitHub?"
> - **Git** = the version control *software* (runs locally, tracks history)
> - **GitHub** = a *hosting platform* for Git repositories (adds collaboration features: PRs, issues, permissions)
> - Alternatives to GitHub: GitLab, Bitbucket. The Git commands are identical regardless of hosting platform.

---

## 2. How Git Stores Data: Snapshots, Not Diffs

### Traditional VCS (delta-based):
```
V1: "hello world"
V2: diff → added "!"        (stores only the change)
V3: diff → replaced "world" with "git"

To reconstruct V3: start at V1 → apply diff1 → apply diff2 → result
```

### Git (snapshot-based):
```
V1: [full snapshot of all files]
V2: [full snapshot of all files]   ← captures EVERYTHING at this moment
V3: [full snapshot of all files]

To see V3: just open the V3 snapshot. Done.
```

**Why snapshots?** Speed. Git doesn't have to replay a chain of diffs to show you a version. It just opens the snapshot. (In practice, Git is smart enough to not store unchanged files twice — it uses pointers — so it's not as storage-heavy as it sounds.)

> **Interview depth:** Git uses a content-addressable filesystem. Every object (commit, tree, blob) is identified by a SHA-1 hash of its content. This makes Git extremely fast at comparisons and guarantees data integrity.

---

## 3. Setup & Configuration

### Installing Git
```bash
# Verify installation
git --version        # should show version number like "git version 2.x.x"
```

### First-time configuration (do this ONCE per machine)
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Verify
git config --list
```

**Why configure?** Every commit records *who* made it. Git uses these credentials to stamp your identity on every snapshot. `--global` means it applies system-wide (not just one project).

---

## 4. Essential Terminal Commands (Quick Reference)

| Command | What it does | Example |
|---------|-------------|---------|
| `ls` | List files in current folder | `ls` |
| `ls -la` | List ALL files (including hidden) with details | `ls -la` |
| `cd folder_name` | Move into a folder | `cd my-project` |
| `cd ..` | Move up one level | `cd ..` |
| `touch filename` | Create a new empty file | `touch index.html` |
| `rm filename` | Delete a file | `rm styles.css` |
| `mkdir folder` | Create a new folder | `mkdir css` |
| `clear` | Clear terminal screen | `clear` |

---

## 5. The Git Workflow — The Three Areas

This is the **most important mental model** in Git. Every file lives in one of three places:

```
┌─────────────────┐      git add      ┌─────────────────┐     git commit     ┌─────────────────┐
│  WORKING TREE   │ ──────────────────►│  STAGING AREA   │ ──────────────────►│   .git (REPO)   │
│                 │                    │                 │                    │                 │
│ Your actual     │                    │ "Review zone"   │                    │ Permanent       │
│ files on disk   │                    │ before commit   │                    │ history/        │
│                 │◄──────────────────│                 │                    │ snapshots       │
│                 │   git restore      │                 │                    │                 │
│                 │   --staged         │                 │                    │                 │
└─────────────────┘                    └─────────────────┘                    └─────────────────┘
```

### Why does the staging area exist?

It gives you a **review checkpoint** before permanently saving. Imagine you changed 5 files but only want to commit 3 of them — staging lets you pick exactly which changes go into the next snapshot.

**Analogy:** Writing an email (working tree) → moving it to "drafts" to review (staging) → hitting "send" (commit).

---

## 6. Core Git Commands — The Daily Workflow

### Initialize a repository
```bash
git init
# Creates a hidden .git folder — Git now watches this project
```

### Check what's happening
```bash
git status
# Shows: which branch you're on, what's modified, what's staged, what's untracked
```

**Status indicators in VS Code:**
- `U` = **Untracked** (Git doesn't know about this file yet)
- `M` = **Modified** (Git is tracking it, but you've changed it since last commit)
- `A` = **Added** (newly staged)

### Stage changes (working tree → staging area)
```bash
git add index.html              # stage one specific file
git add styles.css script.js    # stage multiple files
git add .                       # stage EVERYTHING (use with caution)
```

> **Best practice at work:** stage files *explicitly* by name. `git add .` can accidentally stage files you didn't intend (secrets, debug logs, etc.).

### Unstage changes (staging area → working tree)
```bash
git restore --staged index.html   # "I changed my mind, don't include this in the next commit"
```

### Commit (staging area → permanent history)
```bash
git commit -m "Added navigation header"
#            └── the commit message (MUST be meaningful)
```

**What a commit contains:**
- A unique **hash** (SHA-1) — e.g., `a3f2b1c` — identifies this commit forever
- **Author** name + email
- **Timestamp**
- **Message** (your description of what changed)
- **Snapshot** of all tracked files at this moment

### View history
```bash
git log              # full history (press 'q' to exit)
git log --oneline    # compact one-line-per-commit view
```

### See differences between commits
```bash
git diff <commit_hash_1> <commit_hash_2>
# Green lines = added, Red lines = deleted
```

### Discard all uncommitted changes (nuclear option for working tree)
```bash
git restore .        # throws away ALL changes since last commit
git restore index.html  # discard changes in one specific file only
```

> **Warning:** `git restore .` is irreversible for unstaged changes. The code is gone. Use carefully.

---

## 7. Undoing Commits — `revert` vs `reset`

This is where most beginners get confused. Both undo changes, but in fundamentally different ways.

### The Scenario
```
Commit 1 → Commit 2 → Commit 3 → Commit 4 (has a bug!)
                                      ↑
                                  You are here. Website broken.
```

You want to undo Commit 4's changes. Two approaches:

---

### `git revert` — The Safe Undo (History Preserved)

```bash
git revert <commit_4_hash>
```

**What happens:**
```
Commit 1 → Commit 2 → Commit 3 → Commit 4 (bug) → Commit 5 (undoes Commit 4)
                                                        ↑
                                                   NEW commit that
                                                   reverses the damage
```

- Creates a **new commit** that undoes the bad commit's changes
- The bad commit **stays in history** (you can always see what went wrong and when)
- **Safe for shared/pushed branches** — doesn't rewrite history

> **When to use:** on public branches (main, develop) where other people have already pulled the code. Never rewrite shared history.

---

### `git reset` — The Time Machine (History Rewritten)

#### `git reset --soft <commit_hash>`
```bash
git reset --soft <commit_3_hash>
```

**What happens:**
```
Commit 1 → Commit 2 → Commit 3    ← HEAD moves here
                          ↑
                     Commit 4 is DELETED from history
                     BUT its changes are in your STAGING AREA
                     (you get one more chance to review)
```

- Commit is **removed** from history
- Changes are placed in **staging area** (not lost)
- You can modify them and re-commit, or discard them

#### `git reset --hard <commit_hash>`
```bash
git reset --hard <commit_3_hash>
```

**What happens:**
```
Commit 1 → Commit 2 → Commit 3    ← HEAD moves here
                          ↑
                     Commit 4 is DELETED from history
                     AND its changes are GONE FOREVER
```

- Commit is **removed** from history
- Changes are **permanently deleted** — no staging area, no second chance
- **Dangerous.** Use with extreme caution.

---

### Comparison Table

| | `git revert` | `git reset --soft` | `git reset --hard` |
|---|---|---|---|
| History preserved? | ✅ Yes (adds a new commit) | ❌ No (removes commit) | ❌ No (removes commit) |
| Changes recoverable? | N/A (original still in history) | ✅ Yes (in staging area) | ❌ No (gone forever) |
| Safe for shared branches? | ✅ Yes | ⚠️ Risky | ⚠️ Very risky |
| Creates new commit? | ✅ Yes | ❌ No | ❌ No |
| **Use when** | Undoing on public/pushed code | Undoing locally, want to review | Undoing locally, 100% sure you don't need it |

> **Interview gold:** "When would you use revert vs reset?"
> **Strong answer:** "Use `revert` on shared branches — it's safe because it doesn't rewrite history. Use `reset` only on local unpushed commits where rewriting history won't affect anyone else. `--soft` if I want to re-examine the changes, `--hard` only when I'm absolutely certain the code is garbage."

---

## 8. `git stash` — The Temporary Shelf

### The Problem
You're halfway through a feature (code is messy, not ready to commit). Your boss says: *"Drop everything, fix this critical bug on another branch NOW."*

Options:
- ❌ Commit unfinished work → pollutes history with incomplete snapshots
- ❌ Switch branch without saving → lose all your work
- ✅ **Stash it** → temporarily shelve your work, do the hotfix, come back and continue

### How It Works

```
┌─────────────────┐                    ┌──────────────┐
│  WORKING TREE   │  ──git stash──►    │    STASH     │  (safe temporary box)
│  (messy WIP)    │                    │  (your work) │
└─────────────────┘                    └──────────────┘
         ↑                                     │
         │            ◄──git stash pop──       │
         └─────────────────────────────────────┘
```

### Commands

```bash
git stash              # save current work to the stash, clean the working tree
# ... go fix the bug on another branch ...
# ... come back to your branch ...

git stash pop          # retrieve stashed work AND delete it from stash (most common)
git stash apply        # retrieve stashed work BUT keep it in stash (in case you need it again)
git stash clear        # manually empty the stash
git stash list         # see what's in the stash
```

**`pop` vs `apply`:**
- `pop` = get it back + remove from stash (one action)
- `apply` = get it back + stash still has a copy (need `git stash clear` to clean up)

> **Daily life:** `git stash` → switch branch → fix bug → switch back → `git stash pop` → continue working. You'll do this multiple times a week.

---

## 9. `git commit --amend` — Fix Your Last Commit

### Scenario 1: Typo in commit message
```bash
git commit --amend -m "Correct commit message here"
# Rewrites the LAST commit's message (generates new hash)
```

### Scenario 2: Forgot to include a file
```bash
# You committed but forgot to stage styles.css
git add styles.css
git commit --amend --no-edit
# Adds styles.css into the LAST commit without changing the message
```

> **Warning:** `--amend` rewrites history (creates a new hash). Only use on commits that **haven't been pushed yet**. If you amend a pushed commit, you'll have to force-push — which causes pain for teammates who already pulled the old version.

---

## 10. Branching — The Core of Team Collaboration

### Why Branches Exist

```
MAIN BRANCH (live website / production code)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   C1 ──── C2 ──── C3 ──── C4
                              ↑
                         LIVE WEBSITE is running this code
```

**The rule:** you NEVER push directly to `main`. Why?
- If your code has a bug → the live website breaks → users are affected → revenue is lost
- If 10 people push simultaneously → conflicts everywhere → chaos

**The solution:** everyone works on their own **branch**:

```
MAIN:       C1 ──── C2 ──── C3 ──── C4 ─────────────── C5 (merged!)
                              │                           ↑
FEATURE:                      └──── A ──── B ────────────┘
                                                    (reviewed, tested, safe → merge)
```

Each developer:
1. Creates a branch from `main`
2. Does all their work there (multiple commits)
3. Gets it reviewed & tested
4. Merges it back into `main` only when approved

### Branch Commands

```bash
git branch                    # list all branches (* = current)
git checkout -b add-styles    # CREATE new branch AND switch to it
git checkout main             # switch to existing branch
git branch -d add-styles      # delete branch (safe — won't delete unmerged work)
git branch -D add-styles      # force delete (even if unmerged)
```

### Key Insight: Branches Share History

When you create a branch, you get the **entire history** of the project up to that point:

```bash
# On main with commits C1, C2
git checkout -b feature       # creates 'feature' branch
git log                       # shows C1, C2 (full history inherited!)
```

Changes on one branch are **invisible** from other branches:
```bash
# On 'feature' branch — create styles.css, commit
git checkout main             # switch back
ls                            # styles.css is GONE (it only exists on 'feature')
git checkout feature          # switch back
ls                            # styles.css is BACK
```

This is how branches provide **isolation** — you can experiment freely without affecting anyone else.

---

## 11. Git Merge — Combining Branch Work

### The Goal
Take completed work from your feature branch and incorporate it into `main`.

### How to Merge

```bash
# Step 1: Be on the branch you want to merge INTO (the destination)
git checkout main

# Step 2: Merge the source branch
git merge feature-branch
```

### What Happens Visually

```
BEFORE MERGE:
main:       C1 ──── C2 ──── C3
                     │
feature:             └──── A ──── B

AFTER `git merge feature-branch` (from main):
main:       C1 ──── C2 ──── C3 ──── M (merge commit)
                     │               ↑
feature:             └──── A ──── B ─┘
```

**Key point:** a **merge commit (M)** is created. This commit:
- Has no code changes of its own
- Simply records: "at this point, branch X was merged into main"
- Preserves the full branching history in the project

---

## 12. Merge Conflicts — When Git Needs Your Help

### When Do Conflicts Happen?

A conflict occurs when **the same line(s) in the same file** have different changes on two branches:

```
MAIN branch — index.html line 11:
<p>This is from main</p>

FEATURE branch — index.html line 11:
<span>This is from feature</span>
```

Git says: *"Both branches modified line 11 differently. I don't know which version to keep. Human, help me!"*

### What a Conflict Looks Like

When you run `git merge` and a conflict occurs, Git marks the file:

```html
<<<<<<< HEAD
<p>This is from main</p>
=======
<span>This is from feature</span>
>>>>>>> feature-branch
```

| Marker | Meaning |
|--------|---------|
| `<<<<<<< HEAD` | Start of YOUR current branch's version |
| `=======` | Divider between the two versions |
| `>>>>>>> feature-branch` | End of the INCOMING branch's version |

### How to Resolve

1. **Open the conflicted file**
2. **Remove ALL conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`)
3. **Decide what the final code should be:**
   - Keep current branch's version? Keep incoming? Keep both? Write something entirely new? It's YOUR decision.
4. **Stage the resolved file:** `git add <filename>`
5. **Commit:** `git commit -m "Resolved merge conflict"`

```bash
# After manually fixing the file:
git add index.html
git commit -m "Resolve merge conflict in index.html"
```

> **Real-world tip:** modern editors (VS Code) show buttons like "Accept Current", "Accept Incoming", "Accept Both" above each conflict. These just automate the marker removal — but for complex conflicts, you'll still need to think about what the right code should be.

---

## 13. Git Rebase — The Alternative to Merge

### The Core Idea

Both `merge` and `rebase` accomplish the same goal: **get your branch's work onto main**. The difference is *how the history looks afterward*.

### Merge Creates a Branch Point in History

```
After git merge:
main:    C1 ── C2 ── C3 ─────── M (merge commit)
                │                ↑
feature:        └── A ── B ─────┘

History: non-linear (you can see the branch existed)
```

### Rebase Creates a Linear History

```
After git rebase:
main:    C1 ── C2 ── C3 ── A' ── B'

History: linear (as if the branch never existed)
```

Rebase **takes your commits and replays them on top of the latest main**, one by one.

### The Rebase Process (Step by Step)

```bash
# Step 1: Be on YOUR feature branch
git checkout feature-branch

# Step 2: Rebase onto main (replay your commits on top of main's latest)
git rebase main
# If conflicts → resolve → git add <file> → git rebase --continue
# Repeat for each commit that conflicts

# Step 3: Switch to main and fast-forward
git checkout main
git rebase feature-branch    # (or: git merge feature-branch — it'll be a fast-forward)
```

### Rebase with Conflicts — The Multi-Step Process

Because rebase replays commits **one at a time**, you may need to resolve conflicts at EACH step:

```
Feature has commits: A, B, C
Rebasing onto main...

Step 1: Apply A on top of main
         → Conflict? Resolve it → git add . → git rebase --continue
Step 2: Apply B on top of (main + A)
         → Conflict? Resolve it → git add . → git rebase --continue
Step 3: Apply C on top of (main + A + B)
         → Conflict? Resolve it → git add . → git rebase --continue
Done!
```

### Merge vs Rebase — Complete Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│                    GIT MERGE                                      │
├─────────────────────────────────────────────────────────────────┤
│ • Creates a MERGE COMMIT                                         │
│ • History shows branches existed (non-linear)                    │
│ • Safe for shared/public branches                                │
│ • One-step process (resolve all conflicts at once)               │
│ • Never rewrites history                                         │
│                                                                   │
│ main:  C1 ── C2 ── C3 ──────── M                                │
│              │                  ↑                                 │
│ feat:        └── A ── B ───────┘                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    GIT REBASE                                     │
├─────────────────────────────────────────────────────────────────┤
│ • NO merge commit created                                        │
│ • History is LINEAR (branch disappears)                          │
│ • Dangerous on shared branches (rewrites history)                │
│ • Multi-step process (resolve conflicts per commit)              │
│ • Rewrites commit hashes                                         │
│                                                                   │
│ main:  C1 ── C2 ── C3 ── A' ── B'                               │
│                                                                   │
│ (branch is dissolved — history looks like one straight line)     │
└─────────────────────────────────────────────────────────────────┘
```

### Which Should You Use?

| Situation | Use |
|-----------|-----|
| Merging a feature into `main` on a team | Usually **merge** (preserves context) |
| Keeping your feature branch up-to-date with main | Often **rebase** (cleaner history) |
| Public/shared branch that others have pulled | **NEVER rebase** (rewrites history = breaks others) |
| Solo project, want clean linear history | **Rebase** is fine |
| Team preference? | **Ask your team.** This is a team decision, not a technical one. |

> **The golden rule of rebase:** Never rebase commits that have been pushed and shared with others. Rebase is for cleaning up *local* unpushed history.

---

## 14. Remote Repositories & GitHub

### Local vs Remote

```
┌──────────────────────┐                    ┌──────────────────────┐
│    LOCAL REPO        │                    │    REMOTE REPO       │
│    (your machine)    │ ◄──── git pull ────│    (GitHub)          │
│                      │ ──── git push ────►│                      │
│    .git folder       │                    │    Hosted online     │
│    full history      │                    │    full history      │
│    your branches     │                    │    shared branches   │
└──────────────────────┘                    └──────────────────────┘
```

### Connecting Local to Remote

```bash
# 1. Create a repo on GitHub (github.com → New Repository)

# 2. Link your local repo to it
git remote add origin https://github.com/username/repo-name.git
#                 ↑              ↑
#            nickname       the actual URL
#     (convention: "origin")

# 3. Verify the connection
git remote -v
# origin  https://github.com/... (fetch)
# origin  https://github.com/... (push)
```

**Why "origin"?** It's just a nickname (alias) for the remote URL. You *could* call it anything, but `origin` is the universal convention for "the primary remote."

### Push (local → remote)

```bash
# First time (establishes tracking connection with -u):
git push -u origin main
#         ↑    ↑     ↑
#   "track" remote  branch

# After first time (Git remembers where to push):
git push
```

**`-u` (or `--set-upstream`):** tells Git "from now on, when I say `git push` on this branch, push to `origin/main` automatically." Only needed once per branch.

### Pull (remote → local)

```bash
git pull origin main
# Fetches new commits from remote AND merges them into your local branch
```

**What `git pull` actually does:** it's `git fetch` (download changes) + `git merge` (integrate them) combined into one command.

### The Push/Pull Flow

```
You make local changes → git add → git commit → git push → appears on GitHub

Someone else pushes to GitHub → you do git pull → their changes appear locally
```

**Key rule:** changes do NOT sync automatically. You must explicitly `push` and `pull`.

---

## 15. Collaboration Models

### Model 1: Collaborators (Small Teams)

```
Repo Owner adds team members as "Collaborators"
  → Settings → Collaborators → Add people

Collaborators can:
  ✅ Clone the repo
  ✅ Push directly to branches
  ✅ Create branches
  ✅ Merge PRs

Best for: small trusted teams (5–15 people)
```

### Model 2: Fork + Pull Request (Open Source / Large Teams)

When you **can't** be added as a collaborator (open source, strangers), you use the **fork workflow**:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FORK + PR WORKFLOW                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  1. FORK: Copy the repo to YOUR GitHub account                       │
│                                                                       │
│     Original repo                    Your fork                        │
│     (owner/project) ──── fork ────► (you/project)                    │
│                                                                       │
│  2. CLONE: Download your fork to your machine                        │
│                                                                       │
│     Your fork ──── git clone ────► Your local machine                │
│                                                                       │
│  3. BRANCH: Create a feature branch                                  │
│                                                                       │
│     git checkout -b my-feature                                        │
│                                                                       │
│  4. WORK: Make changes, commit                                        │
│                                                                       │
│     edit files → git add → git commit                                │
│                                                                       │
│  5. PUSH: Push to YOUR fork                                           │
│                                                                       │
│     git push origin my-feature                                        │
│                                                                       │
│  6. PULL REQUEST: Ask the original owner to accept your changes      │
│                                                                       │
│     GitHub shows "Compare & Pull Request" button                     │
│     → Click it → describe your changes → Create PR                  │
│                                                                       │
│  7. REVIEW: Owner reviews your code                                   │
│     → Approves → Merges into their repo                              │
│     → OR requests changes → you fix & push more commits             │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Why Forking is Safe

- The original repo is **never affected** by your fork
- Even if you delete everything in your fork — original is fine
- The owner only accepts changes they explicitly approve (via PR merge)
- This is how thousands of strangers safely contribute to Linux, React, VS Code, etc.

> **Interview question:** "Walk me through how you'd contribute to an open-source project."
> **Strong answer:** "Fork → clone → branch → make changes → commit → push to my fork → open a PR against the original repo → address review feedback → maintainer merges."

---

## 16. Pull Requests (PRs) — The Code Review Gateway

A PR is **not** a Git feature — it's a **GitHub/GitLab feature** built on top of Git.

**What a PR does:**
1. Shows the **diff** (what you changed) in a nice visual format
2. Allows **discussion** (comments on specific lines)
3. Enables **code review** (teammates approve or request changes)
4. Provides a **merge button** (maintainer decides when to incorporate)

**PR best practices (what teams expect):**
- Meaningful title and description
- Small, focused changes (one feature per PR)
- All tests passing
- Respond to review comments promptly
- Follow the project's contribution guide (usually in `README.md` or `CONTRIBUTING.md`)

---

## 17. Complete Command Reference

### Setup & Config
```bash
git init                          # initialize a new git repo
git config --global user.name ""  # set username
git config --global user.email "" # set email
git config --list                 # view current config
```

### Daily Workflow
```bash
git status                        # what's modified/staged/untracked?
git add <file>                    # stage specific file
git add .                         # stage everything
git commit -m "message"           # commit staged changes
git log                           # view commit history
git log --oneline                 # compact history
git diff <hash1> <hash2>          # see changes between two commits
```

### Undoing Things
```bash
git restore .                     # discard all working tree changes
git restore <file>                # discard changes in one file
git restore --staged <file>       # unstage a file (staging → working tree)
git revert <hash>                 # undo a commit (safe, creates new commit)
git reset --soft <hash>           # remove commit, keep changes staged
git reset --hard <hash>           # remove commit AND changes permanently
git stash                         # temporarily shelve changes
git stash pop                     # retrieve shelved changes (and clear stash)
git stash apply                   # retrieve but keep in stash
git commit --amend -m "new msg"   # fix last commit's message
git commit --amend --no-edit      # add staged files to last commit
```

### Branching
```bash
git branch                        # list branches (* = current)
git checkout -b <name>            # create + switch to new branch
git checkout <name>               # switch to existing branch
git branch -d <name>              # delete branch (safe)
git branch -D <name>              # force delete branch
```

### Merging & Rebasing
```bash
# MERGE (from the destination branch):
git checkout main
git merge <branch>                # merge branch into main

# REBASE (from the feature branch):
git checkout feature
git rebase main                   # replay feature commits on top of main
# If conflicts: resolve → git add . → git rebase --continue
git checkout main
git rebase feature                # fast-forward main to include feature
```

### Remote & Collaboration
```bash
git remote add origin <url>       # connect to remote
git remote -v                     # verify connection
git push -u origin main           # first push (sets tracking)
git push                          # subsequent pushes
git pull origin main              # fetch + merge from remote
git clone <url>                   # download a repo from GitHub
```

---

## 18. Visual Summary: The Full Git Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                           │
│   YOUR MACHINE                              │         GITHUB              │
│                                              │                            │
│   ┌──────────┐    git add    ┌─────────┐    │    ┌──────────────────┐    │
│   │ WORKING  │──────────────►│ STAGING │    │    │  REMOTE REPO     │    │
│   │  TREE    │               │  AREA   │    │    │  (origin/main)   │    │
│   └──────────┘               └────┬────┘    │    └────────┬─────────┘    │
│        ↑                          │         │             │              │
│        │ git restore              │ git     │             │              │
│        │ git stash pop            │ commit  │    git push │  git pull    │
│        │                          ▼         │             │              │
│   ┌──────────┐              ┌──────────┐    │             │              │
│   │  STASH   │              │  LOCAL   │◄───┼─────────────┘              │
│   │  (temp)  │              │  REPO    │────┼──────────────────────►     │
│   └──────────┘              │  (.git)  │    │          git push          │
│                              └──────────┘    │                            │
│                                              │                            │
└──────────────────────────────────────────────┴────────────────────────────┘
```

---

## 19. Interview Questions — Test Yourself

**Conceptual:**
1. What's the difference between Git and GitHub?
2. Why do we never push directly to `main`?
3. Explain the three areas (working tree, staging, .git) and why the staging area exists.
4. When would you use `revert` vs `reset`?
5. What's the difference between `merge` and `rebase`? When would you use each?
6. What is a merge conflict and how do you resolve one?
7. Explain the fork + PR workflow for open-source contributions.
8. What does `git stash` solve?

**Practical:**
1. You committed a typo in your message. How do you fix it? → `git commit --amend -m "..."`
2. You accidentally staged a secret file. How do you unstage it? → `git restore --staged <file>`
3. Your last commit broke everything. How do you undo it safely? → `git revert <hash>` (if pushed) or `git reset --soft HEAD~1` (if local)
4. You're mid-feature and need to switch branches urgently. What do you do? → `git stash` → switch → fix → switch back → `git stash pop`
5. A conflict occurs during rebase. Walk me through resolution. → edit file, remove markers, decide on code, `git add`, `git rebase --continue`, repeat for each conflicting commit.

**Deep follow-ups interviewers love:**
- "What happens to commit hashes after a rebase?" → They change (rebase rewrites history, creating new hashes for replayed commits)
- "Why is force-pushing dangerous?" → It rewrites remote history; anyone who already pulled the old version will have conflicts
- "What's a fast-forward merge?" → When main has no new commits since you branched; Git just moves the pointer forward (no merge commit needed)
- "What's HEAD?" → A pointer to the current commit you're on (the "you are here" marker)

---

## 20. Key Takeaways (The Mental Model)

1. **Git tracks snapshots, not diffs** — makes it fast to jump to any version.
2. **Three areas:** working tree → staging → .git (commit). The staging area is your review checkpoint.
3. **Commits are permanent snapshots** identified by unique hashes, with author + message + timestamp.
4. **Branches are cheap and fast** — use them for everything. Never work on `main` directly.
5. **Merge preserves history** (merge commit); **rebase linearizes history** (replays commits). Both get code from branch → main.
6. **Conflicts happen when the same lines differ between branches** — Git marks them, you decide, then continue.
7. **`revert` = safe undo** (new commit); **`reset` = history rewrite** (use locally only).
8. **Push = upload; Pull = download.** Nothing syncs automatically.
9. **Fork + PR = open-source safety** — anyone can propose changes without risking the original.
10. **At your job:** branch → commit → push → PR → code review → merge. Every single day.

---

> These notes cover Git from zero to collaboration-ready. The next topics (Tailwind CSS, React, backend) will use this Git/GitHub workflow as the foundation for all project work.
