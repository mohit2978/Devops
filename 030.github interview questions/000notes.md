# Advanced Git & GitHub Engineering Guide: SDE-2 Interview Preparation & Architectural Mastery

---

## 1. Executive Summary & Curriculum Gap Analysis

In modules **027**, **028**, and **029**, foundational Git and GitHub commands were introduced:
- **027.git and github-1**: Local workspace basics (`init`, `add`, `commit`, `log`, `status`, `diff`, `restore`, `rm`, file lifecycle).
- **028.git and github-2 branching**: Standard branch creation (`branch`, `checkout`, `switch`, `branch -d`), basic merges (`git merge`), and workspace shelving (`git stash`).
- **029.git and github-3 github**: Remote collaboration primitives (`remote add`, `push`, `pull`, `clone`, `fork`, GitHub UI pull requests).

While sufficient for day-to-day Junior Developer tasks, **SDE-2 (Mid-to-Senior Software Engineer)** interviews evaluate system internals, branching governance, disaster recovery, security incident response, and performance debugging.

### Summary of Missed Topics Required for SDE-2

| Domain | What was Covered in 027–029 | What is Missing (Crucial SDE-2 Requirements) |
| :--- | :--- | :--- |
| **Branch Integration** | Basic 2-way merge | **Git Merge vs. Git Rebase** deep dive, Fast-Forward vs. 3-Way (ORT), Interactive Rebase (`-i`), The Golden Rule of Rebasing, PR merge strategies (Squash vs. Rebase vs. Merge Commit). |
| **Git Internals** | High-level working vs staging | **Content-Addressable Object Storage**, Directed Acyclic Graph (DAG), SHA-1/SHA-256 hashes, the 4 objects (**Blob, Tree, Commit, Annotated Tag**), packfiles, plumbing commands (`cat-file`, `hash-object`). |
| **History Rewriting & Undo** | `git restore`, `git checkout` | **`git reset` (--soft, --mixed, --hard) vs. `git revert`**, `git commit --amend`, recovering "lost" commits using **`git reflog`**. |
| **Safety & Push Policies** | `git push -u origin main` | **`git push --force` vs. `git push --force-with-lease`**, branch protection rules, CODEOWNERS file, signed commits (GPG/SSH). |
| **Advanced Tooling** | `git diff`, basic `git stash` | **`git bisect`** for regression hunting (automated with test runners), `git cherry-pick`, `git stash branch/pop/apply`, pre-commit hooks. |
| **Enterprise Branching** | Simple feature branching | **Trunk-Based Development (TBD)** with Feature Flags vs. **GitFlow** vs. GitHub Flow. |
| **Security & Incident Response** | `.gitignore` | **Scrubbing accidentally leaked production secrets/keys** from entire Git history (`git-filter-repo`, BFG Repo-Cleaner) and key revocation workflows. |

---

## 2. Architectural Deep Dive: Git Merge vs. Git Rebase

Branch integration is one of the most heavily scrutinized areas in technical interviews.

![Git Merge vs Git Rebase Visualized](./git-merge-vs-rebase-visualized.svg)

---

### Formal Definitions

#### 1. Git Merge (3-Way Integration)
> **Definition:** `git merge <branch>` combines the histories of two diverged branches by computing a 3-way merge between the common ancestor commit and the two branch tips. If the branches have diverged, it synthesizes a new **Merge Commit** having two (or more) parent pointers. It preserves exact historical chronology without altering any existing commit SHAs.

#### 2. Git Rebase (Linear History Replay)
> **Definition:** `git rebase <base-branch>` rewinds the current feature branch to its common ancestor with the target base branch, temporarily stashes the feature branch's unique commits, advances the feature branch pointer to the current tip of the target base branch, and sequentially reapplies (re-plays) each stashed commit on top of the base. Each replayed commit receives a **completely new commit SHA**.

---

### Detailed Comparison: Merge vs. Rebase

| Architectural Dimension | Git Merge (`git merge`) | Git Rebase (`git rebase`) |
| :--- | :--- | :--- |
| **Commit Hashes** | **Preserved**. All original commit SHAs remain unchanged. | **Rewritten**. Every replayed commit gets a brand-new SHA-1/SHA-256. |
| **Graph Topology** | **Non-linear DAG**. Retains distinct branch lines, fork points, and merge nodes. | **Strictly Linear**. Appears as though all work was performed sequentially on a single line. |
| **Merge Commit** | **Yes** (creates an explicit merge commit `M` with 2 parents, unless fast-forward). | **No**. Commits are appended sequentially without generating extra merge nodes. |
| **Conflict Resolution** | **Single pass**. Conflicts between branches are resolved once inside the merge commit. | **Iterative pass**. Conflicts may need to be resolved commit-by-commit as each is replayed. |
| **Auditability & Traceability** | High fidelity. Shows exact timestamps and branch divergence history. | Clean and readable. Easy to run `git log` and `git bisect`, but obscures original timeline. |
| **Remote Safety** | **100% safe** on shared/public remote branches (`main`, `develop`). Non-destructive. | **Unsafe** if pushed to shared remote branches. Requires force-pushing on feature branch. |

---

### End-to-End Walkthrough: Merge vs. Rebase

Assume `main` has advanced with commit `C3`, while `feature` has two commits `F1` and `F2` branched from `C2`.

```
Initial diverged state:
main:    C1 ─── C2 ─── C3
                 \
feature:          F1 ─── F2
```

#### Approach A: Git Merge
When you run:
```bash
git checkout main
git merge feature
```

1. Git identifies common ancestor `C2`.
2. Git compares `C3` with `F2` using recursive/ORT 3-way merge strategy.
3. If conflicts arise, you resolve them and commit.
4. Git creates commit `M` whose parents are `C3` and `F2`.

```
Resulting Merge Graph:
main:    C1 ─── C2 ─── C3 ─────── M (HEAD -> main)
                 \               /
feature:          F1 ────────── F2
```

#### Approach B: Git Rebase
When you run:
```bash
git checkout feature
git rebase main
```

1. Git identifies common ancestor `C2`.
2. Git saves commits `F1` and `F2` as temporary patches into `.git/rebase-apply/`.
3. Git moves the `feature` branch pointer forward to `C3`.
4. Git applies `F1` on top of `C3` $\rightarrow$ results in `F1'` (new SHA).
5. Git applies `F2` on top of `F1'` $\rightarrow$ results in `F2'` (new SHA).
6. The old commits `F1` and `F2` become unreferenced (eligible for garbage collection).

```
Resulting Rebase Graph:
main:    C1 ─── C2 ─── C3
                         \
feature:                  F1' ─── F2' (HEAD -> feature)
```

Now, bringing `main` up to date is a simple zero-conflict **Fast-Forward Merge**:
```bash
git checkout main
git merge feature   # Fast-forward: main simply moves to F2'
```

---

### The Golden Rule of Rebasing
> **"Never rebase a branch that has been pushed to a remote and is shared with other developers."**

#### Why?
When you rebase, Git creates new commit SHAs (`F1'`), discarding the original SHAs (`F1`). If teammate Bob has pulled your original branch and written code on top of `F1`, your rebase forces you to run `git push --force`. When Bob pulls again, Git sees two completely diverged branches containing duplicate logical changes with different hashes, resulting in repeated merge conflicts and history corruption.

#### Local Feature Branch Rebase Workflow (The SDE-2 Standard):
1. Keep local feature branches rebased against latest `main`:
   ```bash
   git fetch origin
   git rebase origin/main
   ```
2. Resolve any conflicts locally on your isolated branch.
3. Push to your private remote feature branch using lease protection:
   ```bash
   git push origin feature/auth-api --force-with-lease
   ```

---

### GitHub Pull Request Merge Strategies

When closing a PR in GitHub/GitLab, three options exist:

```
+----------------------------------------------------------------------------------------------------+
| 1. Create a merge commit (git merge --no-ff)                                                      |
|    - Appends all individual feature commits + 1 merge commit.                                      |
|    - PROS: Retains full history and author metadata.                                               |
|    - CONS: Can make main branch git log noisy and cluttered.                                      |
+----------------------------------------------------------------------------------------------------+
| 2. Squash and merge (git merge --squash)                                                           |
|    - Condenses all N commits on feature branch into exactly 1 clean commit on main.                |
|    - PROS: Completely eliminates WIP/typo commits ("fix typo", "test"). Perfect for small PRs.     |
|    - CONS: Discards granular incremental commit history.                                           |
+----------------------------------------------------------------------------------------------------+
| 3. Rebase and merge (git rebase + fast-forward)                                                    |
|    - Replays each commit individually onto main in a flat line without a merge commit.            |
|    - PROS: Clean linear history with full granular commits.                                        |
|    - CONS: Modifies commit SHAs; if any commit broke builds, git bisect must check every commit.   |
+----------------------------------------------------------------------------------------------------+
```

---

## 3. Git Internals & Architectural Object Model

Git is fundamentally **not** a file delta tracker (unlike SVN or CVS). Git is a **content-addressable filesystem** built on a Directed Acyclic Graph (DAG) of immutable cryptographic objects.

![Git Internals Objects & Architecture](./git-internals-objects-architecture.svg)

---

### The Three Trees of Git

1. **Working Directory (Filesystem)**:
   - The actual files uncompressed on disk that you edit with your IDE.
2. **Staging Area / Index (`.git/index`)**:
   - A binary cache file containing a sorted list of path names, permissions, and SHA-1/SHA-256 hashes of the files intended for the next commit.
3. **The Object Store (`.git/objects`) & HEAD**:
   - Permanent database storing immutable zlib-compressed objects keyed by their 40-character hexadecimal cryptographic hash.

---

### The Four Core Git Object Types

Every object in Git is stored in `.git/objects/` under a folder named after the first 2 characters of its SHA hash, with the filename being the remaining 38 characters.

#### 1. Blob (Binary Large Object)
- **Stores**: Pure file contents in compressed format (zlib).
- **Does NOT store**: Filenames, timestamps, or directory paths.
- **De-duplication**: If ten identical 5MB files exist in different directories, Git stores **only one blob** (all point to the same SHA).

Inspect a blob using Git plumbing commands:
```bash
# Hash a file and see its SHA
git hash-object README.md
# e69de29bb2d1d6434b8b29ae775ad8c2e48c5391

# Inspect the type of an object
git cat-file -t e69de2
# blob

# Inspect the contents of an object
git cat-file -p e69de2
# # Project Readme Content
```

#### 2. Tree Object
- **Stores**: Directory structures, file modes (e.g., `100644` for normal file, `100755` for executable, `040000` for subdirectory), object types, SHA pointers, and the human-readable filenames.
- A Tree can reference multiple Blobs and child Subtrees.

```bash
git cat-file -p HEAD^{tree}
# Output:
# 100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    README.md
# 100644 blob 3b18e512c019a2872390141bb0203f009941a823    pom.xml
# 040000 tree 8c9d1a10052341498b3f46f3458117769910ba45    src
```

#### 3. Commit Object
- **Stores**:
  1. Pointer to the root **Tree** object representing the project snapshot at that instant.
  2. Pointer(s) to parent commit(s) (zero for initial commit, one for regular commits, two or more for merge commits).
  3. Author name, email, and timestamp.
  4. Committer name, email, and timestamp.
  5. The commit message.

```bash
git cat-file -p HEAD
# Output:
# tree a1b2c3d4e5f6789012345678901234567890abcd
# parent 4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e
# author Alice <alice@enterprise.com> 1727400000 +0000
# committer Alice <alice@enterprise.com> 1727400000 +0000
# 
# feat(auth): add JWT token validation filter
```

#### 4. Annotated Tag Object
- **Stores**: A pointer to a commit object, tagger metadata, optional GPG signature, and release message. Unlike lightweight tags (which are plain text pointer files in `.git/refs/tags/`), annotated tags are permanent objects in the `.git/objects/` store.

---

## 4. History Manipulation & Disaster Recovery: Reset, Revert, and Reflog

![Git Reset vs Revert Lifecycle](./git-reset-vs-revert-lifecycle.svg)

---

### `git reset` vs `git revert`

#### The 3 Modes of `git reset`

`git reset` adjusts where the current branch pointer (`HEAD`) points. The three flags control what happens to the Index and Working Directory:

| Reset Flag | Moves HEAD? | Modifies Index (Staging)? | Modifies Working Directory? | Typical SDE-2 Use Case |
| :--- | :---: | :---: | :---: | :--- |
| **`--soft`** | **Yes** | **No** (files remain staged) | **No** (files remain intact) | Undo commit to reword message, split commits, or combine multiple commits. |
| **`--mixed`** *(default)* | **Yes** | **Yes** (staged files unstaged) | **No** (changes stay on disk) | Undo commit and unstage changes to selectively re-stage files. |
| **`--hard`** | **Yes** | **Yes** (Index cleared) | **Yes** (Files overwritten) | Completely throw away bad experimental work. **Destructive!** |

```bash
# Example 1: Soft reset (Undo commit, keep code staged)
git reset --soft HEAD~1

# Example 2: Mixed reset (Default, keeps modifications in working tree)
git reset HEAD~1

# Example 3: Hard reset (Destroys all uncommitted and committed changes since HEAD~1)
git reset --hard HEAD~1
```

---

### `git revert` (Safe for Production)
`git revert <commit-hash>` does not alter history. Instead, it inspects the diff introduced by `<commit-hash>`, computes the exact inversion (deletions become additions, additions become deletions), and creates a **brand-new forward commit**.

```bash
# Revert a bad commit
git revert 9f8a2b3

# Revert without immediately committing (allows grouping multiple reverts)
git revert -n 9f8a2b3
git revert -n 4c2e1f0
git commit -m "chore: rollback v2.4 billing changes"
```

#### Reverting a Merge Commit
A merge commit has two parents. If you run `git revert <merge-commit>`, Git throws an error: `fatal: commit is a merge but no -m option was given`.
You must specify the mainline parent (`-m 1` keeps the changes of parent 1, usually `main`):
```bash
git revert -m 1 <merge-commit-hash>
```

---

### Disaster Recovery with `git reflog`

> **The Reflog Law:** When you delete a branch or run `git reset --hard`, Git does **not** instantly delete your commits. Commits remain in the local object store for typically 30 to 90 days until Git runs garbage collection (`git gc`).

Every time `HEAD` moves (commit, checkout, rebase, reset, merge), Git appends an entry to `.git/logs/HEAD`.

#### Practical Recovery Scenario: Rescuing an Accidental `git reset --hard`
Suppose you ran `git reset --hard HEAD~3` and accidentally wiped 3 critical commits:

```bash
# 1. Inspect the reference log
git reflog

# Sample Output:
# 3b4a2c1 (HEAD -> main) HEAD@{0}: reset: moving to HEAD~3
# 8f9c1d2 HEAD@{1}: commit: feat: complete oauth integration
# 5a2e9b0 HEAD@{2}: commit: test: add unit tests for token validation
# 1d4e7f8 HEAD@{3}: commit: feat: initial oauth scaffolding
# 9a8b7c6 HEAD@{4}: checkout: moving from feature to main

# 2. Identify the commit right before the accidental reset: 8f9c1d2 (HEAD@{1})

# 3. Restore your branch pointer to that exact commit:
git reset --hard 8f9c1d2
# OR create a brand-new recovery branch pointing to it:
git branch recovery-branch 8f9c1d2
```

---

## 5. Enterprise Git Workflows & Advanced SDE-2 Practices

### Trunk-Based Development vs. GitFlow

In enterprise software engineering (especially high-throughput Microservices and Continuous Delivery):

```
+----------------------------------------------------------------------------------------------------+
| TRUNK-BASED DEVELOPMENT (TBD) - Preferred for Modern Cloud-Native CI/CD                           |
| - Single shared branch ("main" or "trunk").                                                        |
| - Developers create short-lived feature branches (< 1-2 days lifecycle).                           |
| - Frequently merged into main via PRs with automated CI testing.                                   |
| - Incomplete features are protected behind Feature Flags (Toggles), NOT long-lived branches.        |
| - Enables multiple deployments per day; eliminates painful multi-week "merge hell".                |
+----------------------------------------------------------------------------------------------------+
| GITFLOW - Suited for Traditional Scheduled Release Cadences (e.g., On-Premises, Embedded)         |
| - Multiple persistent branches: main, develop, release/*, feature/*, hotfix/*.                    |
| - High governance and verification at stage boundaries.                                            |
| - Heavier overhead; slow feedback loops; prone to massive merge conflicts during release merges.   |
+----------------------------------------------------------------------------------------------------+
```

---

### Automated Bug Isolation with `git bisect`

When a regression or bug is discovered in production, identifying which commit introduced it across hundreds of commits manually is tedious. `git bisect` executes a binary search ($O(\log N)$) through the commit history.

#### Automated Bisect with Maven / JUnit:
```bash
# 1. Start bisect mode
git bisect start

# 2. Mark the current broken commit as 'bad'
git bisect bad HEAD

# 3. Mark the last known healthy commit as 'good'
git bisect good v2.1.0
# Git reports: Bisecting: 64 revisions left to test after this (roughly 6 steps)

# 4. Automate the test runner across commits!
git bisect run mvn test -Dtest=OrderServiceTest#testOrderPlacement

# Git automatically checks out intermediate commits, runs the test,
# evaluates the exit code (0 = good, non-zero = bad), and outputs:
# 7c4e9f2a819b is the first bad commit
# Author: Dev <dev@company.com>
# Date:   Wed Sep 24 14:20:00 2026
# Commit message: refactor: change order currency conversion

# 5. Clean up and return to your original branch
git bisect reset
```

---

### Security Incident Response: Scrubbing Leaked Secrets

> **Question:** An engineer committed an active AWS Access Key or Database Password and pushed it to GitHub. What is the immediate and complete incident response?

1. **Step 1 (Immediate - Containment)**:
   - **Revoke and rotate the compromised credential immediately** in the AWS Console / Vault / IAM. Do not assume deleting the commit makes it secure; web crawlers index public GitHub commits in under 3 seconds.
2. **Step 2 (Local & Remote History Rewrite)**:
   - Running `git rm secret.txt && git commit` will **NOT** remove the secret from Git history (the blob remains in previous commits).
   - Use the officially recommended modern tool `git-filter-repo` (replacing the deprecated and slow `git filter-branch`):
     ```bash
     # Install git-filter-repo
     pip install git-filter-repo

     # Remove a sensitive file from the entire commit history
     git-filter-repo --path credentials.env --invert-paths

     # OR replace occurrences of secret text in any file across all commits
     echo "AKIAIOSFODNN7EXAMPLE==>REDACTED" > replace.txt
     git-filter-repo --replace-text replace.txt
     ```
3. **Step 3 (Force Push with Coordination)**:
   ```bash
   git push origin --force --all
   git push origin --force --tags
   ```
4. **Step 4 (GitHub Cache Invalidation)**:
   - Contact GitHub Support to perform a cache purge (`git gc` / ref purge) on their servers so cached pull request views and orphan commit URLs no longer display the secret.

---

### Safe Force Pushing: `--force` vs. `--force-with-lease`

| Command | Behavior | Risk Level |
| :--- | :--- | :--- |
| `git push --force` (`-f`) | Overwrites the remote branch blindly with your local branch pointer, regardless of whether someone else pushed commits to the remote in the meantime. | **Extremely Dangerous**. Destroys teammates' work without warning. |
| `git push --force-with-lease` | Checks if the remote tracking ref (`origin/feature`) matches the state of the remote branch. If someone else pushed a commit to the remote, the push is **aborted**. | **Standard Enterprise Best Practice**. Protects concurrent work. |

---

## 6. Top 15 SDE-2 Git & GitHub Interview Questions & Master Answers

### Q1: In team collaboration, when should you choose `git merge` versus `git rebase`?
**Answer:**
- **Use `git rebase`**:
  - On your **local, private feature branches** to sync up with changes from `origin/main` before opening or merging a PR.
  - Using interactive rebase (`git rebase -i`) to squash messy commits ("wip", "fixed lint", "typo") into logical, self-contained atomic units before code review.
- **Use `git merge`**:
  - On **shared, public branches** (`main`, `release`, `develop`).
  - When you want to preserve the exact chronological context and true historical record of when a feature branch was integrated.
  - When closing high-governance PRs where maintaining an immutable merge commit with associated review approvals is required.

---

### Q2: What exactly happens under the hood during a rebase, and why do commit hashes change?
**Answer:**
In Git, a commit hash is a cryptographic SHA calculated from:
1. The root tree SHA (the file snapshot).
2. The parent commit SHA(s).
3. The author and committer metadata (name, email, timestamp).
4. The commit message.

During `git rebase`, Git lifts the feature branch commits and replays them on top of the target branch's tip. Because the parent commit SHA changes, the resulting cryptographic hash **must** change. Additionally, the committer timestamp is updated to the current time, guaranteeing a new hash even if the code diff was identical.

---

### Q3: What is the difference between `git push --force` and `git push --force-with-lease`?
**Answer:**
- `git push --force` is an unconditional destructive command. It tells the remote repository to set its branch pointer to whatever your local branch points to, overwriting whatever commits were on the remote. If a teammate pushed a commit while you were working, their commit is orphaned and permanently lost from the branch.
- `git push --force-with-lease` acts like an atomic compare-and-swap (CAS) operation. It checks whether the remote branch has received any updates that you have not yet fetched locally. If another developer pushed commits to the remote branch, Git rejects your push, prompting you to `git fetch` and inspect their changes first.

---

### Q4: Explain `git reset --soft`, `--mixed`, and `--hard`. If you execute `git reset --hard` by mistake, how do you recover?
**Answer:**
- `--soft`: Moves `HEAD` to the target commit. Does not touch the Index (staging area) or the Working Directory. All modifications stay staged, ready for `git commit`.
- `--mixed` (default): Moves `HEAD` and resets the Index to match the target commit. The Working Directory is left untouched. Changes appear as unstaged modifications.
- `--hard`: Moves `HEAD`, resets the Index, and overwrites the Working Directory to match the target commit. Any uncommitted work in the working tree is permanently lost.
- **Recovery**: If commits were previously committed, they can be recovered using `git reflog`. Find the SHA of `HEAD` immediately before the reset (e.g., `HEAD@{1}`) and run `git reset --hard <SHA>` or `git checkout -b rescue-branch <SHA>`.

---

### Q5: Does Git store file differences (deltas) or full snapshots?
**Answer:**
Git primarily stores **full snapshots**, not file deltas.
When you stage and commit a modified file:
1. Git compresses the entire file content into a new **Blob** object.
2. The new **Tree** object points to this new Blob for the modified file, while continuing to point to existing unchanged Blobs for other files.
3. However, to save disk space and network bandwidth, Git periodically executes packfile generation (`git gc` / pack-objects). Git analyzes similar files and packs them into a compressed packfile (`.pack`) using delta compression (where one file is stored as a base and another as a difference), along with an index (`.idx`) for fast random lookup.

---

### Q6: An engineer pushed a production secret to GitHub. What are the remediation steps?
**Answer:**
1. **Immediate Revocation**: Treat the secret as compromised. Immediately revoke and rotate the secret in the cloud console or key vault.
2. **History Scrubbing**: Do not just commit a deletion. Use `git-filter-repo` (or BFG Repo-Cleaner) to rewrite the repository history and remove the secret or file across all commits and branches:
   ```bash
   git-filter-repo --replace-text expressions.txt
   ```
3. **Coordinated Force Push**: Force push the sanitized branches and tags to the remote (`git push --force --all && git push --force --tags`).
4. **Platform Cache Invalidation**: Contact GitHub/GitLab support to purge dangling commit caches and reflogs so the commit cannot be viewed via direct commit URL.
5. **Team Re-clone**: Instruct all team members to re-clone or reset their local tracking branches to prevent them from re-pushing the old dirty history.

---

### Q7: How do you revert a Merge Commit without breaking future branch integrations?
**Answer:**
Because a merge commit has multiple parents, you must specify the mainline parent flag `-m <parent-number>`:
```bash
git revert -m 1 <merge-commit-hash>
```
Parent 1 represents the mainline branch (e.g., `main`), while Parent 2 is the branch that was merged in.

**Critical Caveat for Future Merges:**
Reverting a merge commit reverses the code changes, but the original feature branch commits remain in the history of `main`. If you later attempt to re-merge the feature branch after making fixes, Git will consider the earlier commits as already merged and will omit them! To fix this, you must **revert the revert commit** before merging the revised feature branch:
```bash
git revert <revert-commit-hash>
git merge feature-branch
```

---

### Q8: What are GitHub's three PR merge strategies, and when should you use each?
**Answer:**
1. **Create a merge commit (`--no-ff`)**:
   - Preserves all individual feature commits and links them via a merge commit.
   - *Use case*: Long-running feature branches where keeping individual commit milestones and author history is critical.
2. **Squash and merge (`--squash`)**:
   - Condenses all PR commits into a single commit on the target branch.
   - *Use case*: Standard daily development where developers create messy WIP commits ("fix typo", "address review"). Keeps the main branch history clean and atomic.
3. **Rebase and merge**:
   - Appends all individual PR commits onto the tip of the target branch without a merge commit.
   - *Use case*: Teams that demand a strictly linear commit history while preserving individual commit granularity. Requires high discipline to ensure every commit passes builds and tests.

---

### Q9: How does `git bisect` work, and how can it be integrated into CI/CD or automated scripts?
**Answer:**
`git bisect` performs a binary search between a known "good" commit and a known "bad" commit to identify the exact commit that introduced a bug in $O(\log N)$ evaluations.
It can be fully automated using `git bisect run <script>`:
- The provided script must return exit code `0` if the commit is good/passes.
- It must return exit code `1` through `127` (except 125) if the commit is bad/fails.
- It can return exit code `125` if the commit cannot be tested (e.g., compile error unrelated to test) so Git skips it.
Example: `git bisect run npm test` or `git bisect run ./gradlew test`.

---

### Q10: What is Trunk-Based Development, and why do high-performing DevOps teams prefer it over GitFlow?
**Answer:**
- **Trunk-Based Development (TBD)** is a source-control branching model where all developers merge small, frequent updates into a single core branch ("trunk" or "main"), typically multiple times a day.
- Feature branches are short-lived (hours or 1–2 days).
- Incomplete features in progress are deployed to production hidden behind **Feature Flags / Toggles**.
- **Advantages over GitFlow**:
  - Eliminates long-lived branches that accumulate massive, painful merge conflicts.
  - Dramatically improves Lead Time for Changes and Deployment Frequency (key DORA metrics).
  - Enables true Continuous Integration: code is continuously integrated and tested against the master branch rather than tested in isolation for weeks.

---

### Q11: What is a "Detached HEAD" state, what causes it, and how do you save work created in it?
**Answer:**
- **Definition**: Normally, `HEAD` points to a branch name (symbolic reference), which in turn points to a commit. A "Detached HEAD" occurs when `HEAD` points directly to a commit hash instead of a branch reference.
- **Cause**: Running `git checkout <commit-hash>` or `git checkout origin/main`.
- **Implication**: Any commits you make while in detached HEAD are not associated with any branch. If you checkout another branch, these commits become orphan objects and will eventually be permanently deleted by `git gc`.
- **How to save work**: Simply create a new branch pointing to your current detached HEAD commit:
  ```bash
  git branch my-new-feature
  git switch my-new-feature
  ```

---

### Q12: How does `git cherry-pick` work under the hood, and what are its risks?
**Answer:**
- **Mechanism**: `git cherry-pick <commit-hash>` calculates the diff introduced by `<commit-hash>` relative to its immediate parent, and applies that diff as a brand new commit on top of your current `HEAD`.
- **Common Use Cases**:
  - Porting an urgent hotfix from `main` into an active release branch (`release/v1.2`).
  - Extracting a single useful utility commit from an abandoned feature branch.
- **Risks**:
  - Generates duplicate commits with different SHAs representing the exact same logical change.
  - Can cause merge conflicts when the original branch is eventually merged into the target branch.
  - Bypasses normal pull request verification if not carefully reviewed.

---

### Q13: What does `git stash` do internally, and where is the data stored?
**Answer:**
When you execute `git stash`:
1. Git does not create temporary files outside the repository. Instead, it creates **special commit objects** in the `.git/objects/` store:
   - One commit storing the state of the Staging Area / Index.
   - One commit storing the state of the Working Directory.
   - (If `-u` is used) One commit storing untracked files.
2. Git references these commits in `.git/refs/stash`.
3. The reflog for stashes is maintained at `.git/logs/refs/stash`.
4. Running `git stash pop` restores the trees from those commit objects and updates the stash reflog.

---

### Q14: What is the difference between Git Submodules and Git Subtrees?
**Answer:**
- **Git Submodule**:
  - Points to a specific commit SHA of an external repository via a `.gitmodules` file.
  - Does not store the external repository's contents directly inside the parent repo.
  - Requires explicit commands (`git submodule update --init --recursive`) to clone and update.
  - Can easily become detached or desynchronized across team members.
- **Git Subtree**:
  - Embeds the external repository's files and history directly into a subfolder of the host repository.
  - Teammates do not need any special tools; a standard `git clone` gets all files.
  - Updates require using `git subtree pull --prefix=<dir> <repo> <branch>`.

---

### Q15: What are Git Hooks and how can an engineering organization enforce pre-commit checks?
**Answer:**
Git hooks are custom bash/python scripts located in `.git/hooks/` that execute automatically before or after Git lifecycle events:
- **Client-Side Hooks**:
  - `pre-commit`: Runs static analysis, linting (`eslint`, `spotless`), and unit tests before a commit is created.
  - `commit-msg`: Validates commit message formatting (e.g., enforcing Jira ticket prefixes like `[PROJ-1234] feat: ...`).
  - `pre-push`: Runs integration tests or prevents pushing to protected branch names directly.
- **Team-Wide Enforcement**:
  - Because `.git/hooks/` is local and not checked into version control, enterprise projects use tools like **Husky** (Node.js) or **pre-commit** (Python) which configure Git's `core.hooksPath` to point to a version-controlled directory (e.g., `.husky/` or `.githooks/`).
- **Server-Side Hooks** (GitHub Actions / GitLab CI):
  - Client-side hooks can be bypassed using `git commit --no-verify`. Therefore, security policies, secret scanning, and branch protections must always be enforced on the remote server via CI/CD pipelines.

---

## 7. Recommended SDE-2 Interview Cheat Sheet

| Command / Scenario | Solution |
| :--- | :--- |
| **Combine last 3 commits into 1** | `git rebase -i HEAD~3` $\rightarrow$ mark 2nd and 3rd as `squash` (or `s`). |
| **Fix a typo in the last commit message** | `git commit --amend -m "new message"` |
| **Add a forgotten file to the last commit** | `git add forgotten.txt && git commit --amend --no-edit` |
| **Discard local unstaged file changes** | `git restore <file>` |
| **Discard local staged file changes** | `git restore --staged <file>` |
| **Discard all local changes (staged & unstaged)** | `git reset --hard HEAD` |
| **Recover commit discarded by hard reset** | `git reflog` $\rightarrow$ find hash $\rightarrow$ `git reset --hard <hash>` |
| **Bring main updates into feature branch cleanly** | `git checkout feature && git rebase main` |
| **Push rebased branch safely to remote** | `git push origin feature --force-with-lease` |
| **Revert a production merge commit** | `git revert -m 1 <merge-commit-hash>` |
| **Find bug in 1,000 commits automatically** | `git bisect start HEAD v1.0 && git bisect run <test-script>` |
| **Scrub accidentally pushed secret** | Revoke secret immediately $\rightarrow$ `git-filter-repo` $\rightarrow$ `git push --force --all` |
