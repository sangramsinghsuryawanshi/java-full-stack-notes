## 1. Introduction to Rebase and Squash

### Definition

**Rebase** and **Squash** are advanced Git features used to rewrite commit history.

- **Rebase** moves a sequence of commits to a new base commit, creating a perfectly linear history.
    
- **Squash** takes multiple separate commits and compresses them into a single, cohesive commit.
    

### Purpose

As projects grow, commit histories can become chaotic, resembling a tangled web of overlapping branches. Rebase and Squash are repository maintenance tools. They ensure that the project timeline remains clean, readable, and strictly linear, making code reviews easier and making tools like `git bisect` (for bug hunting) much more effective.

### 5 Levels of Explanation

- **Level 1 – Beginner:** Squashing is like taking 5 messy rough drafts and turning them into 1 final, polished essay. Rebasing is like moving your place in line to the very back so you have the most up-to-date information before you submit your work.
    
- **Level 2 – Student:** If `main` has moved forward while you were working on your feature branch, `git rebase main` takes your new commits and reapplies them on top of the newest `main`. `git squash` (via interactive rebase) lets you combine "wip" (work in progress) commits before submitting an assignment.
    
- **Level 3 – Developer:** Rebase temporarily saves your branch's commits, updates your branch's base pointer to the target branch's `HEAD`, and replays your commits one by one. Because the parent commit changes, Git generates entirely new SHA-1 hashes for your commits.
    
- **Level 4 – Senior Developer:** We enforce a linear history policy. Merging feature branches with standard 3-way merges creates unnecessary "diamond" shapes in the DAG (Directed Acyclic Graph). Developers must rebase their feature branches against `main`, resolve conflicts locally, and then Fast-Forward merge or Squash-Merge via Pull Requests.
    
- **Level 5 – Architect:** **The Golden Rule of Rebasing:** _Never rebase a branch that has been pushed to a shared public repository._ Rewriting public history destroys the integrity of the distributed DAG, causing catastrophic synchronization failures for the rest of the engineering team. Rebase is strictly a local workspace sanitation tool.
    

## 2. Core Concepts

- **Base Commit:** The commit from which your branch originated.
    
- **Replaying:** The process Git uses during a rebase to apply your changes sequentially onto the new base.
    
- **Interactive Rebase (`-i`):** A powerful Git tool that opens a text editor, allowing you to manually pick, drop, reword, edit, or squash commits before they are replayed.
    
- **Squash:** Combining a commit with the one previous to it.
    
- **Fixup:** Like a squash, but it discards the commit message of the squashed commit, keeping only the older commit's message.
    
- **Linear History:** A commit graph that forms a single straight line, with no visible merge commits branching out and back in.
    

## 3. Internal Working & Architecture

Rebasing abandons your old commits and creates brand new ones.

### ASCII Architecture Diagram: Rebase

**Before Rebase (Diverged History):**

Plaintext

```
          A --- B --- C (Feature Branch)
         /
D --- E --- F --- G (Main Branch)
```

**After `git rebase main` (Linear History):**

Plaintext

```
                    A' --- B' --- C' (Feature Branch - NEW SHAs)
                   /
D --- E --- F --- G (Main Branch)
```

_(Notice how the original A, B, and C are gone, replaced by A', B', and C' sitting on top of G)._

### Interactive Merge vs. Rebase vs. Squash Simulator

To truly master history rewriting, interact with this visualizer to see how the Directed Acyclic Graph (DAG) changes under different operations:

## 4. Syntax & Commands

### Basic Syntax

Bash

```
# Rebase current branch onto main
git rebase main

# Abort a rebase if conflicts get too complicated
git rebase --abort

# Continue rebase after resolving a conflict
git rebase --continue
```

### Advanced Syntax (Interactive Rebase)

Bash

```
# Start an interactive rebase for the last 3 commits
git rebase -i HEAD~3
```

## 5. Examples

### Example 1: Intermediate (Standard Rebase)

You are on `feature-login`. `main` was updated by your team.

Bash

```
git fetch origin
git rebase origin/main
# (If conflicts occur, fix them, git add ., then git rebase --continue)
git push --force-with-lease origin feature-login
```

### Example 2: Production Ready (Interactive Squash)

You made 3 messy commits on your branch. You want to squash them into 1 clean commit before opening a Pull Request.

**Step 1:** Run `git rebase -i HEAD~3`

**Step 2:** Git opens a text editor looking like this:

Plaintext

```
pick 1a2b3c4 Add login UI
pick 4d5e6f7 Fix CSS padding
pick 7g8h9i0 Oops, fix typo in HTML
```

**Step 3:** Change the word `pick` to `squash` (or `s`) for the commits you want to fold upwards:

Plaintext

```
pick 1a2b3c4 Add login UI
squash 4d5e6f7 Fix CSS padding
squash 7g8h9i0 Oops, fix typo in HTML
```

**Step 4:** Save and close. Git will pop up another editor to let you write one final, unified commit message.

## 6. Internal Memory Diagram (.git/rebase-merge)

When a rebase starts, Git creates a temporary directory to manage the state.

Plaintext

```
.git/
├── rebase-merge/         -> Temporary folder tracking the rebase
│   ├── done              -> Commits already replayed
│   ├── git-rebase-todo   -> The script of commits to pick/squash
│   └── head-name         -> Name of the branch being rebased
└── objects/              -> New Blob, Tree, and Commit objects are generated here
```

Once the rebase finishes, the `.git/rebase-merge/` folder is deleted automatically.

## 7. Execution Flow (Line-by-Line Rebase)

When you execute `git rebase main` on the `feature` branch:

1. Git finds the common ancestor of `main` and `feature`.
    
2. It takes a "diff" snapshot of every commit on `feature` since that ancestor.
    
3. It stores these snapshots in a temporary area.
    
4. It forcefully moves the `feature` branch pointer to exactly match the tip of `main`.
    
5. It begins applying the temporary snapshots one by one (replaying).
    
6. If it hits a conflict, it pauses and waits for user input (`git rebase --continue`).
    

## 8. Real Project Usage

- **Open Source Development:** Maintainers of projects like Linux or React will absolutely reject Pull Requests that have 20 "WIP" commits. Contributors are expected to use interactive rebase to squash their work into logical, atomic commits before submitting.
    
- **Microservices CI/CD:** To keep release notes readable, engineering teams enforce "Squash and Merge" policies on GitHub. When a PR is approved, GitHub automatically squashes the branch into a single commit titled with the Jira ticket number (e.g., `[PAY-901] Add Stripe Integration`).
    

## 9. Best Practices

- **The Golden Rule:** Never rebase commits that exist outside your local repository and that people may have based work on. Only rebase your local, private feature branches.
    
- **Use `--force-with-lease`:** If you rebase a branch that you already pushed to GitHub, your local history and GitHub's history will mismatch. You must force push to overwrite GitHub. Always use `git push --force-with-lease` instead of `-f`. It checks if teammates added new commits to the remote branch and aborts if they did, preventing data loss.
    
- **Rebase Frequently:** If a feature takes two weeks, rebase against `main` every single morning. Resolving 1 day of conflicts is easy; resolving 14 days of conflicts is a nightmare.
    

## 10. Common Mistakes

- **Mistake:** Rebasing `main` onto `feature` instead of `feature` onto `main`.
    
    - _Result:_ You just rewrote the history of the production branch locally.
        
    - _Fix:_ Run `git reflog` to find the commit hash of `main` before the rebase, and use `git reset --hard <hash>` to undo the mistake.
        
- **Mistake:** Getting stuck in a seemingly endless loop of rebase conflicts.
    
    - _Fix:_ If you are lost, just type `git rebase --abort`. It safely cancels the entire operation and puts your branch exactly back to how it was before you started.
        

## 11. Debugging Guide

- **Error:** `error: could not apply fa391cd... Add new feature`
    
- **Root Cause:** A merge conflict occurred during the rebase replay process.
    
- **Fix:** 1. Open your IDE and resolve the conflict (remove `<<<<<<` markers).
    
    2. Run `git add .` (Do NOT run `git commit`).
    
    3. Run `git rebase --continue`.
    

## 12. Interview Preparation

### Top Questions

**1. Explain the difference between `git merge` and `git rebase`.**

- **Answer:** `git merge` takes two diverged histories and ties them together with a new "Merge Commit", preserving the exact chronological history. `git rebase` rewrites history by moving the base of your branch to the tip of the target branch, creating a clean, linear history without extra merge commits.
    

**2. What is the Golden Rule of Rebasing?**

- **Answer:** Never rebase a public branch. If you rebase commits that other developers have already pulled, Git will see diverging histories. When they try to push, it will cause duplicate commits and massive conflicts.
    

**3. How do you squash multiple commits into one?**

- **Answer:** You can use an interactive rebase (`git rebase -i HEAD~n`), where `n` is the number of commits to review. In the text editor, change the command from `pick` to `squash` for the commits you want to combine. Alternatively, use the "Squash and Merge" button on GitHub PRs.
    

## 13. MCQ Section

**1. What happens to the commit SHAs (hashes) after a successful rebase?**

A) They remain exactly the same.

B) Only the base commit's SHA changes.

C) All rebased commits get brand new SHAs.

D) They are deleted permanently.

_Answer: C. Explanation: Because a commit's hash is calculated based on its contents AND its parent commit, changing the parent (base) results in entirely new hashes._

**2. Which command safely cancels a rebase operation that is currently stuck in a conflict?**

A) git exit

B) git rebase --abort

C) git reset --hard

D) git rebase --stop

_Answer: B. Explanation: `--abort` rolls back the `.git/rebase-merge` state to exactly how it was before the command was initiated._

## 14. Comparison Tables

### Rebase vs. Merge

|**Feature**|**git merge**|**git rebase**|
|---|---|---|
|**History Style**|Non-linear (Diamond shapes)|Linear (Straight line)|
|**Commit Creation**|Creates 1 new Merge Commit|Recreates multiple commits (New SHAs)|
|**History Preservation**|Preserves exact chronological timeline|Rewrites history to look sequential|
|**Best Used For**|Bringing features into `main`|Updating local features with latest `main`|
|**Public Branches**|Highly Recommended|**Strictly Prohibited**|

## 15. Revision Notes & Cheat Sheet

### 5-Minute Revision

- **Rebase:** Moves your branch's starting point to the tip of another branch. Keeps history perfectly straight. NEVER do this on `main` or public branches.
    
- **Squash:** Cleans up your mess. Combines 10 "typo fix" commits into 1 "Feature Complete" commit using Interactive Rebase.
    

### Quick Command Cheat Sheet

| **Command**                   | **Action**                                |
| ----------------------------- | ----------------------------------------- |
| `git rebase main`             | Move current branch base to tip of main   |
| `git rebase -i HEAD~4`        | Open editor to squash/edit last 4 commits |
| `git rebase --continue`       | Move to next step after fixing a conflict |
| `git rebase --abort`          | Panic button. Cancel the rebase entirely  |
| `git push --force-with-lease` | Push a rebased branch to GitHub safely    |