## 1. Git Installation & Setup

Before you start tracking your code, you need to ensure Git is installed and properly configured.

### Check Git Installation

Bash

```
git --version
```

- **Purpose:** Verifies that Git is installed on your system and outputs the current version (e.g., `git version 2.34.1`).
    

### Configure Git Identity

Git bakes your identity into every commit you make. This is crucial for collaborative projects so everyone knows who wrote which line of code.

Bash

```
git config --global user.name "Sangramsinh Suryawanshi"
git config --global user.email "sangram@gmail.com"
```

- **Purpose:** Sets your name and email globally across all repositories on your computer.
    

### Initialize a Repository

Bash

```
git init
```

- **Purpose:** Transforms an ordinary, unversioned directory into a Git repository.
    
- **Internal Working:** This command creates a hidden `.git` folder in your current directory. This hidden folder contains the skeleton structure for your repository's version history, staging area, and configuration.
    

> **Pro Tip:** You can view this hidden folder by running `ls -a` (List all), which displays hidden files like `.git`, `.`, and `..`.

## 2. Essential Linux File Commands

Since Git is heavily used via the Command Line Interface (CLI), mastering these basic Linux/Unix commands is mandatory for any developer.

| **Command**        | **Action**                          | **Example Output / Usage**                          |
| ------------------ | ----------------------------------- | --------------------------------------------------- |
| `pwd`              | **P**rint **W**orking **D**irectory | `/d/projects/my-app`                                |
| `ls`               | **L**i**s**t files and folders      | `index.html script.js styles.css`                   |
| `mkdir <name>`     | **M**a**k**e **dir**ectory          | `mkdir notes` (Creates a folder named 'notes')      |
| `cd <name>`        | **C**hange **d**irectory            | `cd notes` (Moves into the 'notes' folder)          |
| `cd ..`            | Go up one directory level           | Moves you back to the parent folder                 |
| `touch <file>`     | Create an empty file                | `touch text.txt`                                    |
| `cat <file>`       | View file contents                  | `cat text.txt` (Prints text inside the file)        |
| `mv <old> <new>`   | **M**o**v**e or rename a file       | `mv old.txt new.txt`                                |
| `cp <file> <copy>` | **C**o**p**y a file                 | `cp text.txt backup.txt`                            |
| `rm <file>`        | **R**e**m**ove (delete) a file      | `rm text.txt` (Deletes permanently)                 |
| `rm -r <folder>`   | Remove a folder recursively         | `rm -r notes` (Deletes folder and all its contents) |

## 3. The Core Git Workflow

Understanding the Three States of Git is the single most important concept for beginners.

1. **Working Directory:** Your scratchpad. The actual files you are opening, editing, and saving on your computer.
    
2. **Staging Area (Index):** The loading dock. A temporary area where you gather and organize the specific changes you want to include in your next save.
    
3. **Repository (.git):** The vault. The permanent database where Git stores your saved snapshots (commits).
    

### Interactive Git Workflow Simulator

To visualize how files move through these three states, experiment with this simulator:

## 4. Core Git Commands

### Check Status

Bash

```
git status
```

- **Purpose:** Your compass. It tells you exactly what is happening in your repository right now. It shows:
    
    - **Untracked files:** New files Git hasn't seen before.
        
    - **Modified files:** Tracked files that have unsaved changes.
        
    - **Staged files:** Changes ready to be committed.
        

### Add Files (Working Directory → Staging Area)

Bash

```
git add file.txt    # Stages a single file
git add .           # Stages ALL modified and untracked files in the current directory
```

- **Purpose:** Moves your changes into the Staging Area. It tells Git, _"I want these specific changes to be included in my next commit."_
    

### Commit Changes (Staging Area → Repository)

Bash

```
git commit -m "Add login page UI"
```

- **Purpose:** Takes everything in the staging area and permanently saves it as a snapshot in the Git repository.
    
- **Best Practice:** Always write clear, descriptive commit messages in the imperative mood (e.g., "Fix bug" rather than "Fixed bug").
    

### Restore/Unstage Files (Staging Area → Working Directory)

Bash

```
git restore --staged file.txt
```

- **Purpose:** If you accidentally ran `git add` on a file you didn't mean to, this pulls it back out of the Staging Area.
    
- **Important Note:** This **does not** delete your work or change the file's contents; it simply moves it back to the "Modified" state in your Working Directory.