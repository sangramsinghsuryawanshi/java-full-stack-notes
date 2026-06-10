## 1. The Master Git Cheat Sheet

Instead of memorizing a flat list, think of Git commands categorized by their workflow phase.

### A. Setup & Initialization

|**Command**|**Purpose**|**Industry Context**|
|---|---|---|
|`git init`|Initializes a local repository|Creates the hidden `.git` folder tracking local history.|
|`git clone <URL>`|Downloads a remote repository|Pulls code from GitHub/GitLab to your local machine.|

### B. The Daily Workflow (Save & Sync)

|**Command**|**Purpose**|**Industry Context**|
|---|---|---|
|`git status`|Checks repo state|Always run this before adding/committing to avoid mistakes.|
|`git add .`|Stages all changes|Moves code from Working Directory to Staging Area.|
|`git commit -m "msg"`|Saves snapshot|Permanently records staged changes into local history.|
|`git push origin main`|Uploads code|Sends local commits to the remote GitHub server.|
|`git pull origin main`|Downloads & Merges|Grabs teammates' code and integrates it into your workspace.|
|`git log`|Views history|Shows commit hashes, authors, and timestamps.|

### C. Branching & Merging (Collaboration)

|**Command**|**Purpose**|**Industry Context**|
|---|---|---|
|`git branch`|Lists local branches|The branch with the `*` is your current active branch.|
|`git checkout <name>`|Switches branches|Moves the `HEAD` pointer to the specified branch.|
|`git checkout -b <name>`|Creates & Switches|Faster way to start a new feature branch.|
|`git merge <name>`|Combines branches|Pulls `<name>`'s history into your current branch.|

### D. Advanced & Emergency Operations

|**Command**|**Purpose**|**Industry Context**|
|---|---|---|
|`git stash`|Saves WIP code safely|Cleans working directory without committing incomplete work.|
|`git stash pop`|Restores WIP code|Brings stashed code back and deletes the stash entry.|
|`git rebase main`|Flattens history|Moves your feature branch's base to the tip of `main`.|
|`git reset --hard HEAD~1`|⚠️ Deletes last commit|**DANGER:** Destroys your last commit and all uncommitted changes.|

## 2. Git & GitHub Interview Preparation

### Basic & Intermediate Questions

**1. What is Git, and how does it differ from GitHub?**

- **Answer:** Git is a Distributed Version Control System (DVCS) installed locally on your machine to track changes in source code. GitHub is a cloud-based hosting platform (owned by Microsoft) that stores Git repositories, enabling team collaboration, pull requests, and CI/CD pipelines.
    

**2. What is the "Staging Area" (or Index) in Git?**

- **Answer:** It is a temporary preparation zone between your working directory and the repository database. It allows developers to selectively group related file changes together (using `git add`) before saving them as a single cohesive snapshot (using `git commit`).
    

**3. What does `HEAD` mean in Git?**

- **Answer:** `HEAD` is a special internal pointer in Git. It always points to the current branch reference (like `refs/heads/main`), which in turn points to the latest commit you have checked out in your working directory.
    

### Advanced Questions

**4. What is the exact difference between `git pull` and `git fetch`?**

- **Answer:**
    
    - `git fetch` safely downloads the latest commit history from the remote repository to your local `.git` folder, but it **does not** touch your working directory files.
        
    - `git pull` is a combined command. It runs `git fetch` and then immediately runs `git merge` to integrate those downloaded changes into your active branch.
        

**5. Explain `git merge` vs. `git rebase`. When should you use which?**

- **Answer:** Both integrate changes from one branch into another, but they handle history differently.
    
    - **Merge** creates a new "Merge Commit" that ties the two branch histories together. It preserves the exact chronological timeline but can make history look messy (diamond shapes).
        
    - **Rebase** rewrites history by moving the base of your feature branch to the very tip of the target branch. It creates a perfectly linear, readable history but changes commit hashes.
        
    - _Industry Rule:_ Use Rebase to update your local, private feature branches. Use Merge (via Pull Requests) to bring those finished features into the public `main` branch.
        

## 3. Quick Revision & Comparison Tables

### The 1-Minute Conceptual Revision

Read this immediately before walking into an interview:

- **git init** → Create a brand new local repository.
    
- **git add** → Move changes to the staging dock.
    
- **git commit** → Permanently save a snapshot of the dock.
    
- **git push** → Upload your saved snapshots to the cloud.
    
- **git pull** → Download and merge cloud snapshots to your machine.
    
- **Branch** → An independent, parallel universe for your code.
    
- **Merge** → Combine two parallel universes together.
    
- **Fork** → Make a personal server-side copy of someone else's repo.
    
- **Clone** → Download a remote repo to your laptop's hard drive.
    
- **PR (Pull Request)** → Formally ask code owners to merge your changes.
    
- **Rebase** → Rewrite history into a single straight line.
    
- **Stash** → A temporary locker for unfinished, messy code.
    

### High-Yield Comparison Tables

**Merge vs. Rebase**

|**Feature**|**git merge**|**git rebase**|
|---|---|---|
|**Mechanics**|Creates a new Merge Commit|Moves base commits to the tip|
|**History Layout**|Preserves original, branched history|Rewrites history to be linear|
|**Complexity**|Easy (Safe for public branches)|Advanced (Never use on public branches)|

**Pull vs. Fetch**

|**Feature**|**git fetch**|**git pull**|
|---|---|---|
|**Action**|Downloads remote metadata and commits|Downloads commits AND merges them|
|**Working Directory**|Remains completely unchanged|Files are immediately updated/modified|
|**Safety**|100% Safe|Can trigger immediate Merge Conflicts|

## 4. Placement & Career Mentor Notes

- **Resume Tip:** Do not just list "Git" under skills. Add a link to your GitHub profile. Ensure your GitHub has a green contribution graph, and pin your best 3-4 projects.
    
- **What Interviewers Actually Care About:** Interviewers at Product-Based Companies (Amazon, Uber, Swiggy) will not ask you basic syntax. They will give you a scenario: _"You pushed a commit with a hardcoded AWS password to main. Walk me through exactly how you fix this."_ (Answer: Rotate the password immediately in AWS, then use `git filter-repo` or `git rebase -i` to rewrite history, followed by a force push).
    
- **Service-Based Companies:** Focus heavily on the differences between Git/GitHub, Merge/Rebase, and understanding how to resolve merge conflicts in standard Java files.