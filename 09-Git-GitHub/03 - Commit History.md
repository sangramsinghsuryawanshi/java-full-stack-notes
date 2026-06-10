## 1. Viewing Commit History (`git log`)

To understand the lifecycle of a project, you need to be able to read its history. Git provides powerful tools to inspect the timeline of your snapshots.

### Core Commands

**1. Standard Log**

Bash

```
git log
```

- **Purpose:** Displays the complete commit history in chronological order (newest first).
    
- **Output Details:** For every commit, it shows:
    
    - **Commit Hash:** A unique 40-character SHA-1 ID (e.g., `commit 9fceb02d0ae598e95dc970b74767f19372d61af8`)
        
    - **Author:** Name and email of the person who made the change.
        
    - **Date:** Timestamp of the commit.
        
    - **Message:** The descriptive message attached to the commit.
        

**2. Short Log (Industry Standard for Quick Scans)**

Bash

```
git log --oneline
```

- **Purpose:** Condenses each commit to a single line, showing only the first 7 characters of the hash and the commit message.
    
- **Example Output:**
    
    Plaintext
    
    ```
    b12de56 Added Login Page
    a23bc4f Initial Commit
    ```
    

> **Architect Pro-Tip:** In large enterprise projects with dozens of branches, senior developers use: `git log --oneline --graph --decorate --all`. This draws an ASCII tree in your terminal showing how branches diverge and merge!

## 2. Undoing Changes (`git reset`)

### 5 Levels of Explanation

- **Level 1 – Beginner:** `git reset` is an "undo" button for your commits. It lets you go back in time.
    
- **Level 2 – Student:** It moves the `HEAD` (the pointer to your current location in history) back to a previous commit, allowing you to fix mistakes.
    
- **Level 3 – Developer:** `git reset` manipulates the Three Trees of Git (Repository, Staging Area, Working Directory) depending on the flag you use (`--soft`, `--mixed`, `--hard`).
    
- **Level 4 – Senior Developer:** Never use `--hard` on pushed branches. Reset alters the local Git history (rewriting the DAG). If pushed, it will cause sync conflicts for the rest of the team.
    
- **Level 5 – Architect:** Resetting is a local repository mutation. In CI/CD pipelines, we prefer `git revert` (which creates a new commit to undo changes) over `git reset` to maintain an immutable, auditable history for compliance.
    

### The Three Types of Reset

The `~1` syntax means "go back one commit from the current `HEAD`".

#### 1. Soft Reset

Bash

```
git reset --soft HEAD~1
```

- **What it does:** Moves `HEAD` back one commit. **Keeps** your files in the Staging Area.
    
- **Use Case:** You committed too early and want to add a few more files to the _same_ commit.
    

#### 2. Mixed Reset (Default)

Bash

```
git reset HEAD~1 
# or git reset --mixed HEAD~1
```

- **What it does:** Moves `HEAD` back one commit. Empties the Staging Area, but **keeps** your changes in the Working Directory.
    
- **Use Case:** You want to completely reorganize your last commit (e.g., split one big commit into two smaller ones).
    

#### 3. Hard Reset ⚠️ (DANGER ZONE)

Bash

```
git reset --hard HEAD~1
```

- **What it does:** Moves `HEAD` back one commit. **Destroys** all changes in the Staging Area and Working Directory. The code is gone.
    
- **Use Case:** You tried an experiment, it completely broke your app, and you want to wipe it out and return to the last known working state.
    

### Interactive Git Reset Simulator

To truly understand the difference between the three resets, interact with this visualization showing how data moves between the Git zones:

## 3. Interview Preparation: Git Reset

**Q: Explain the difference between `git reset` and `git revert`.**

- **Answer:** `git reset` goes back in time by deleting commits from the current branch's history. It rewrites history. `git revert` undoes the changes of a past commit by creating a _brand new_ commit with the inverse changes.
    
- **Follow-up:** "When would you use which?" Use `reset` for local, unpushed commits to clean up your history. Use `revert` for commits that have already been pushed to a shared remote repository (like GitHub) to avoid disrupting teammates.
    

## 4. Revision Cheat Sheet: Core Git Commands

|**Command**|**Action**|**Zone Affected**|
|---|---|---|
|`git init`|Initializes a new local repo|Creates `.git` folder|
|`git status`|Shows current state of files|Working & Staging|
|`git add .`|Stages all modified/new files|Working → Staging|
|`git commit -m "msg"`|Saves staged files to history|Staging → Repository|
|`git log`|Views full commit history|Repository|
|`git log --oneline`|Views short commit history|Repository|
|`git restore --staged <file>`|Unstages a file|Staging → Working|
|`git reset --soft HEAD~1`|Undoes commit, keeps files staged|Repo → Staging|
|`git reset --hard HEAD~1`|Undoes commit, destroys changes|Repo (Deletes data)|
