## 1. Introduction to Pull Requests (PRs)

### Definition

A Pull Request (PR) is a formal request to merge code changes from one branch (the `head` or feature branch) into another branch (the `base` or main branch). While often associated with Git, **Pull Requests are not a native Git feature**; they are a collaboration mechanism popularized by hosting platforms like GitHub, GitLab, and Bitbucket.

### Purpose

Pull Requests serve as the primary gateway for Code Review and Quality Assurance in modern software development. They prevent unverified, buggy, or malicious code from entering the production (`main`) branch by enforcing a process of peer review, discussion, and automated testing before the merge occurs.

### The Golden Rule of Modern Development

> ❌ **NEVER commit directly to the `main` branch.**
> 
> ✅ **ALWAYS create a Feature Branch and submit a Pull Request.**

### 5 Levels of Explanation

- **Level 1 – Beginner:** A Pull Request is like submitting a rough draft of your essay to a teacher. The teacher reads it, suggests corrections, and once it's perfect, it gets added to the final book.
    
- **Level 2 – Student:** After pushing your feature branch to GitHub, you open a PR. This creates a webpage where your teammates can see exactly which lines of code you added or deleted, leave comments, and eventually approve the merge.
    
- **Level 3 – Developer:** A PR provides a dedicated forum for discussing a specific set of commits. It displays the `diff` between the `base` branch and the `compare` branch. It acts as a gatekeeper: no code is merged until the CI/CD pipeline (tests) passes and at least one peer approves it.
    
- **Level 4 – Senior Developer:** We enforce branch protection rules on `main` that strictly require PRs. A PR should be atomic—addressing only one concern to make reviews effective. We utilize `CODEOWNERS` to automatically route PRs to the relevant domain experts based on the directories modified.
    
- **Level 5 – Architect:** PRs are the instantiation of the GitOps model. A PR is not just about code; it represents a proposed state change to our infrastructure. Approving and merging a PR triggers automated webhook events that initiate immutable container builds and zero-downtime Kubernetes deployments.
    

## 2. Core Concepts

- **Base Branch:** The target branch that will receive the changes (usually `main` or `develop`).
    
- **Compare (Head) Branch:** The branch containing your new changes (e.g., `feature-login`).
    
- **Diff:** The visual representation of changes (additions in green, deletions in red).
    
- **Reviewers:** Teammates requested to evaluate the code.
    
- **Assignee:** The person responsible for driving the PR to completion (usually the author).
    
- **Draft PR:** A PR created to show Work In Progress (WIP) but marked as not yet ready for review.
    
- **Merge Conflicts:** When changes in the PR conflict with new code that was added to the base branch while the PR was open.
    

## 3. Internal Working & Architecture

When you open a PR, GitHub calculates the difference between the tip of your branch and the common ancestor it shares with the base branch.

### ASCII Workflow Diagram

Plaintext

```
Local Machine                          GitHub Server
=================                      ===================
1. git checkout -b feature             4. Open Pull Request (Web UI)
         |                                      |
2. Write Code & Commit                 [ Automated CI/CD Tests Run ]
         |                                      |
3. git push origin feature             [ Peer Review & Comments ]
         |                                      |
         +--------------------------------> 5. PR Approved
                                                |
                                       6. Merged into 'main'
```

### Interactive Pull Request Lifecycle Simulator

To deeply understand the stages of a Pull Request, interact with this simulated lifecycle:

## 4. Syntax & Commands

Since Pull Requests are primarily a GitHub UI feature, you typically create them in the browser. However, using the **GitHub CLI (`gh`)** is becoming the industry standard for fast, terminal-based workflows.

### Basic Workflow (Web UI)

1. Push branch: `git push -u origin feature-login`
    
2. Go to GitHub repository.
    
3. Click the **"Compare & pull request"** button that automatically appears.
    

### Intermediate Workflow (GitHub CLI)

Bash

```
# Push branch and immediately create a PR from the terminal
git push -u origin feature-login
gh pr create --title "Add Login UI" --body "Resolves #42 by adding the JWT login form."
```

### Advanced Syntax (Managing PRs via CLI)

Bash

```
gh pr status           # Check status of your open PRs
gh pr checkout 104     # Checkout a teammate's PR locally to test it
gh pr merge 104        # Merge the PR directly from the terminal
```

## 5. Examples

### Example 1: Beginner (The Standard Flow)

1. Fork a repository.
    
2. Clone it and branch: `git checkout -b fix-typo`.
    
3. Fix the typo, commit, and push.
    
4. Go to the original repo, click "New Pull Request", select your fork as the `head`, and submit.
    

### Example 2: Intermediate (Iterative Review)

1. You submit a PR for a Database Service.
    
2. A Senior Developer leaves a comment: _"Please extract this SQL query into a constant."_
    
3. **Crucial Step:** You do NOT close the PR. You simply make the change locally, commit it (`git commit -m "Extract SQL to constant"`), and `git push`.
    
4. The PR on GitHub automatically updates with the new commit.
    

### Example 3: Production Ready (Squash Merging)

Your feature branch has 15 small commits (`WIP`, `fix bug`, `oops typo`, `actually fix it`).

When merging the PR, the maintainer selects **Squash and Merge**. This condenses all 15 messy commits into one single, clean commit on the `main` branch.

## 6. Execution Flow (Server-Side Merge Types)

When the "Merge Pull Request" button is clicked, GitHub executes one of three Git commands on the server:

1. **Create a Merge Commit:** `git merge --no-ff`. Preserves all commits from the feature branch and creates a distinct merge commit. (Good for complex features).
    
2. **Squash and Merge:** `git merge --squash`. Combines all feature commits into one new commit on the base branch. (Best for keeping `main` history clean).
    
3. **Rebase and Merge:** `git rebase`. Moves the entire feature branch history to the tip of `main` without a merge commit. (Creates a linear history).
    

## 7. Real Project Usage

- **Open Source Frameworks (e.g., React, Spring Boot):** Maintainers receive hundreds of PRs a day. They rely on PR templates (a markdown file that pre-fills the PR description) to ensure contributors provide necessary details like "Steps to Reproduce" and "Checklist".
    
- **Enterprise Service Companies (TCS, Infosys):** PRs are tied directly to Jira tickets. A PR title must begin with the ticket number (e.g., `[PROJ-102] Refactor Payment Gateway`) so project managers can track code progression.
    

## 8. Best Practices

- **Keep PRs Small (Atomic):** A PR with 50 lines of changed code will get a rigorous review. A PR with 2,000 lines will just get a "Looks good to me" (LGTM) because reviewers suffer from fatigue. Aim for < 400 lines of change.
    
- **Write Great Descriptions:** Provide the _Why_, not just the _What_. Include screenshots or GIFs if it's a UI change.
    
- **Self-Review First:** Before adding reviewers, read through your own Diff on GitHub. You will often catch your own missed `console.log()` or commented-out code.
    
- **Draft PRs:** If you need CI/CD to run on your code, but you aren't ready for human review, open the PR as a "Draft".
    

## 9. Common Mistakes

- **Mistake:** Continuing to push entirely unrelated features to a branch that already has an open PR.
    
    - _Result:_ The PR becomes bloated, confusing reviewers.
        
    - _Fix:_ One branch = One Feature = One PR. If you start a new feature, check out `main`, pull the latest, and create a _new_ branch.
        
- **Mistake:** Force pushing (`git push -f`) to a PR branch after a review.
    
    - _Result:_ It destroys the commit history and makes it hard for reviewers to see what you changed since their last review.
        
    - _Fix:_ Just add new commits to address review feedback. They will be squashed at the end anyway.
        

## 10. Security & Performance Considerations

- **Branch Protection Rules:** In GitHub Settings, administrators require PRs before merging to `main`. They also enforce:
    
    - Require at least 1 or 2 approving reviews.
        
    - Require status checks to pass (e.g., SonarQube for code quality, JUnit tests).
        
    - Dismiss stale pull request approvals when new commits are pushed.
        
- **Secret Scanning:** GitHub automatically scans PR diffs for AWS keys or database credentials and blocks the PR from being merged if a secret is found.
    

## 11. Debugging Guide: Resolving PR Conflicts

- **Scenario:** GitHub says "This branch has conflicts that must be resolved."
    
- **Root Cause:** Since you opened your PR, someone else merged code into `main` that modified the exact same lines you are modifying.
    
- **Fix (The Professional Way):**
    
    1. `git checkout main`
        
    2. `git pull origin main` (Get the new code)
        
    3. `git checkout your-feature-branch`
        
    4. `git merge main` (Pull the new `main` code into your feature branch)
        
    5. Resolve the conflicts in your IDE, commit, and `git push`. The PR will automatically turn green.
        

## 12. Interview Preparation

### Top Questions

**1. What is the difference between a Pull Request and a Merge?**

- **Answer:** A Merge is a local Git command (`git merge`) that mathematically combines code histories. A Pull Request is a web-based collaboration layer built _on top_ of Git that facilitates code review, discussion, and automated testing before the actual merge command is executed on the server.
    

**2. Explain what "Squash and Merge" does to a Pull Request.**

- **Answer:** It takes all the individual, potentially messy commits from the feature branch, condenses them into a single comprehensive commit, and applies that one commit to the main branch. This keeps the main branch's history incredibly clean and readable.
    

**3. If you get feedback on a PR, should you close it and open a new one?**

- **Answer:** No. A PR dynamically tracks the feature branch. You simply make the requested changes in your local IDE, commit them, and push them to the same branch. The PR will automatically update with the new commits.
    

## 13. MCQ Section

**1. Why is it considered a bad practice to commit directly to the `main` branch?**

A) Because Git will delete the main branch.

B) Because it bypasses code review, testing, and quality assurance gates.

C) Because it causes the repository to use too much disk space.

D) Because GitHub charges money for direct commits.

_Answer: B. Explanation: Direct commits to main violate the CI/CD pipeline and code review processes._

**2. Which Git command is used to update an already open Pull Request with new changes?**

A) git pr update

B) git pull origin feature

C) git push

D) git update-ref

_Answer: C. Explanation: Pushing new commits to the branch associated with the PR will automatically update the PR on GitHub._

## 14. Comparison Tables

### Direct Merge vs. Pull Request

|**Feature**|**Direct git merge**|**Pull Request (GitHub/GitLab)**|
|---|---|---|
|**Location**|Executed locally|Executed on the hosting platform|
|**Code Review**|None (Immediate merge)|Allows inline commenting and peer review|
|**Testing Gate**|Relies on developer running tests|Enforces CI/CD pipelines (e.g., GitHub Actions)|
|**Access Control**|Requires write access to `main`|Can be done by anyone (via Forking)|

## 15. Revision Notes & Cheat Sheet

### 5-Minute Revision

A **Pull Request (PR)** is how you say, _"Hey team, I finished my feature. Please review my code so we can merge it into main."_

- **The Rule:** Never commit to `main`.
    
- **The Flow:** Branch -> Code -> Commit -> Push -> Open PR -> Review -> Approve -> Merge.
    
- **Iteration:** If changes are requested, don't open a new PR. Just add commits to your local branch and push again. The PR updates magically.
    

### Quick Cheat Sheet (PR Flow)

1. `git checkout -b feature-name` (Create branch)
    
2. `git commit -m "Changes"` (Save work)
    
3. `git push -u origin feature-name` (Upload branch)
    
4. Go to GitHub -> Click **Compare & Pull Request**.
    
5. Assign reviewers and wait for approval.