## 1. Introduction to Git Branching

### Definition

A branch in Git is an independent, parallel line of development. It is essentially a movable pointer to a specific commit. Merging is the process of combining the history of one branch back into another.

### History & Purpose

In older centralized version control systems (like SVN), creating a branch meant copying the entire project directory, which was slow and consumed heavy disk space. Git revolutionized this in 2005. A Git branch is merely a 41-byte file containing the SHA-1 checksum of a commit. This makes Git branching instantaneous and lightweight, encouraging developers to create branches for every feature, bug fix, or experiment.

### 5 Levels of Explanation

- **Level 1 – Beginner:** A branch is like a parallel universe for your project. You can build a new feature without breaking the main working code. When it's perfect, you merge those universes together.
    
- **Level 2 – Student:** Branching isolates your workspace. You use `git branch` to create one, `git checkout` to switch to it, and `git merge` to integrate your code back into the `main` branch.
    
- **Level 3 – Developer:** A branch is a lightweight pointer to a commit. The `HEAD` pointer tells Git which branch you are currently on. Merging algorithmically calculates the differences between the tip of your branch, the tip of the target branch, and their common ancestor.
    
- **Level 4 – Senior Developer:** We enforce branch-based workflows (like GitFlow or GitHub Flow) to ensure continuous integration. `main` is always deployable. Feature branches isolate in-progress code, enabling code reviews via Pull Requests before the merge.
    
- **Level 5 – Architect:** Branching strategies dictate team velocity and CI/CD pipeline design. Long-lived feature branches cause "merge hell" and integrate poorly with Agile. We prefer trunk-based development with short-lived feature branches and feature flags to decouple deployment from release.
    

## 2. Core Concepts

- **Branch:** A named reference to a commit.
    
- **main / master:** The default branch name in Git.
    
- **HEAD:** A special pointer that indicates your current active branch or commit.
    
- **Merge:** The action of bringing changes from one branch into another.
    
- **Fast-Forward Merge:** If the target branch has not diverged (no new commits since you branched off), Git simply moves the pointer forward.
    
- **3-Way Merge (Merge Commit):** If both branches have new commits, Git creates a brand new "Merge Commit" that has two parent commits, tying the histories together.
    

## 3. Internal Working & Architecture

Unlike SVN, Git does not copy files when you branch. It simply creates a new pointer.

### ASCII Architecture Diagram

**Diverged Branching History:**

Plaintext

```
          (Feature Branch)
          C3 --- C4
         /         \
C1 --- C2           C6 (Merge Commit)
         \         /
          C5 ------
          (Main Branch)
```

## 4. Syntax & Commands

### Basic Syntax

Bash

```
# List all local branches (the one with * is active)
git branch

# Create a new branch
git branch <branch-name>

# Switch to a branch
git checkout <branch-name>

# Modern alternative to switch branches (Git 2.23+)
git switch <branch-name>
```

### Intermediate Syntax

Bash

```
# Create and immediately switch to the new branch
git checkout -b <branch-name>
# or modern equivalent
git switch -c <branch-name>

# Merge a branch into your CURRENT branch
git merge <branch-name>

# Delete a branch (only if merged)
git branch -d <branch-name>
```

### Advanced Syntax

Bash

```
# Force delete an unmerged branch
git branch -D <branch-name>

# Rename current branch
git branch -m <new-name>
```

## 5. Examples

### Example 1: Beginner (Creating and Switching)

Bash

```
git branch feature-login
git checkout feature-login
# Work on code...
git add .
git commit -m "Add username field"
```

### Example 2: Intermediate (The Merge Workflow)

You finished the login feature and need to add it to main.

Bash

```
# 1. Switch back to the receiving branch
git checkout main

# 2. Pull latest main code from GitHub (Best Practice)
git pull origin main

# 3. Merge the feature branch into main
git merge feature-login

# 4. Clean up: Delete the local feature branch
git branch -d feature-login
```

## 6. Internal Memory Diagram (.git Directory)

How does Git know about branches? Look inside `.git/refs/heads/`.

Plaintext

```
.git/
├── HEAD               -> Contains: "ref: refs/heads/feature-login"
├── refs/
│   ├── heads/
│   │   ├── main           -> Text file containing hash: a1b2c3d...
│   │   └── feature-login  -> Text file containing hash: e5f6g7h...
```

## 7. Execution Flow (Line-by-Line)

When you execute `git checkout -b feature-login`:

1. Git creates a new file `.git/refs/heads/feature-login`.
    
2. It writes the exact SHA-1 hash of the commit you are currently on into that file.
    
3. It updates the `.git/HEAD` file to point to `refs/heads/feature-login`.
    
4. Your working directory files remain exactly the same.
    

When you execute `git merge feature-login` (while on `main`):

1. Git finds the common ancestor commit of both `main` and `feature-login`.
    
2. It calculates the diffs (changes) introduced in `feature-login`.
    
3. It applies those diffs to the current working tree.
    
4. If no conflicts exist, it creates a new "Merge Commit" and updates the `main` pointer to this new commit.
    

## 8. Real Project Usage

- **E-Commerce Systems:** "Release Branching". Developers merge feature branches into an `integration` branch. Once testing passes, an isolated `release-v1.2` branch is created for final QA. If bugs are found, they are fixed directly on the release branch.
    
- **SaaS Products:** "Hotfix Branching". A critical bug is found on the live server. A branch named `hotfix-db-crash` is created directly from the `main` (production) code, bypassing the normal development queue.
    

## 9. Best Practices

- **Use Naming Conventions:** Prefix branches with their purpose (e.g., `feature/login-ui`, `bugfix/header-crash`, `hotfix/security-patch`, `docs/readme-update`).
    
- **Keep Branches Short-Lived:** The longer a branch lives independently, the harder the inevitable merge conflict will be. Merge back to main within days, not weeks.
    
- **Always Merge INTO the Base:** You merge features _into_ main. You do not merge main into features (use `git rebase` for updating features instead).
    

## 10. Common Mistakes

- **Mistake:** Working directly on the `main` branch without creating a feature branch.
    
    - _Fix:_ If you haven't pushed yet, just run `git checkout -b new-feature`. Your uncommitted work will travel with you to the new branch.
        
- **Mistake:** "Detached HEAD state". This happens when you `git checkout <commit-hash>` instead of a branch name.
    
    - _Fix:_ If you make commits in detached HEAD, they will be lost. Save them by creating a branch right there: `git checkout -b rescue-branch`.
        

## 11. Performance Considerations

- **Time Complexity:** Branch creation is strictly $O(1)$. It does not matter if your repository has 10 files or 10 million files; creating a branch just writes 41 bytes to a text file.
    
- **Space Complexity:** $O(1)$ for branch creation. However, the commits you _make_ on that branch will consume space proportional to the file changes.
    

## 12. Debugging Guide: Merge Conflicts

- **Error:** `CONFLICT (content): Merge conflict in index.html`
    
- **Root Cause:** You modified the exact same line in `index.html` on `main` and on `feature-login`. Git doesn't know which version to keep.
    
- **Fix:**
    
    1. Open `index.html`. You will see markers:
        
        HTML
        
        ```
        <<<<<<< HEAD
        <button>Login Now</button>
        =======
        <button>Sign In</button>
        >>>>>>> feature-login
        ```
        
    2. Manually delete the markers and keep the correct code.
        
    3. Run `git add index.html`.
        
    4. Run `git commit` to finalize the merge.
        

## 13. Interview Preparation

### Top Questions

**1. What is the difference between `git checkout` and `git switch`?**

- **Answer:** `git checkout` is an older, overloaded command used for both switching branches AND restoring files. `git switch` was introduced in Git 2.23 specifically for switching branches to make the commands more intuitive and prevent accidental file deletions.
    

**2. What is a "Detached HEAD"?**

- **Answer:** It occurs when the HEAD pointer is pointing directly to a specific commit hash rather than a branch name. Any new commits made here will not belong to any branch and will be garbage-collected once you switch away.
    

**3. Explain Fast-Forward vs. 3-Way Merge.**

- **Answer:** A fast-forward merge happens when the target branch has no new commits since the feature branch was created; Git just moves the target pointer to the feature branch's tip. A 3-Way merge happens when both branches have diverged, forcing Git to create a new, distinct "Merge Commit" with two parents.
    

## 14. MCQ Section

**1. Which command creates a new branch AND switches to it immediately?**

A) git branch -c feature

B) git checkout feature

C) git checkout -b feature

D) git switch feature

_Answer: C. (Note: `git switch -c feature` is also correct)._

**2. Why are Git branches considered "lightweight"?**

A) They compress the code heavily.

B) They only copy the files that were modified.

C) They do not copy files at all; they are just text files containing a commit pointer.

D) They only exist in RAM.

_Answer: C. Explanation: Git branches are simply 41-byte files in the `.git/refs/heads/` directory._

## 15. Revision Notes & Cheat Sheet

### 5-Minute Revision

Branching isolates risk. `main` is sacred. Always branch off `main`, write your code, and merge it back. When you merge, Git will either fast-forward (if history is linear) or create a merge commit (if history diverged). If Git gets confused on same-line changes, it triggers a merge conflict for you to solve.

### Quick Command Cheat Sheet

| **Command**              | **Action**                            |
| ------------------------ | ------------------------------------- |
| `git branch`             | List branches                         |
| `git checkout -b <name>` | Create & enter branch                 |
| `git switch <name>`      | Switch branch safely                  |
| `git merge <branch>`     | Absorb `<branch>` into current branch |
| `git branch -d <name>`   | Delete local branch                   |