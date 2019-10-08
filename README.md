# Zigvy Git Workflow

## Content
- [Zigvy Git Workflow](#zigvy-git-workflow)
  - [Content](#content)
  - [Setup](#setup)
  - [Straight Forward Workflow](#straight-forward-workflow)
  - [Git workflow when a task is waiting for another one](#git-workflow-when-a-task-is-waiting-for-another-one)
  - [Other scenario](#other-scenario)

## Setup
1. Fork the upstream repo with your account

2. Clone the forked repo using `git clone`
```
git clone https://github.com/your_username/repo_name
```
3. Setup `upstream` remote for updating new code from other members
```
git remote add upstream https://github.com/owner_user/repo_name
```
4. Config name and email for git
```
git config user.name "Your name, can be any name"
git config user.email "Your git email"
```
5. Update all the remote data
```
git remote update
```
6. Pull the latest code from upstream master and push it to your origin master
```
git pull upstream master --rebase
git push origin master
```
7. Now you are ready to go

## Straight Forward Workflow
When you have an issue on github or trello, jira, etc

1. Create and checkout to a new branch
```
git checkout -b branch_name
```
Suggested branch naming convension is:
   - Issue number. i.e: issue_123
   - Feature name. i.e: feature/implement_login
2. Add **all** new files or new update to git tracker
```
git add -A
```
If you don't want to add all files
```
git add file1 file2 file3 ...
```
3. Commit the changes
```
git commit -m "Commit message"
```
4. Pushing the change
```
git push origin your_branch
```
5. **Before creating a new pull request, you must update your current branch with the latest code from upstream master**
```
// get latest code from upstream master
git checkout master
git pull upstream master --rebase

// update your branch with master code
git checkout your_branch
git rebase master

// update the remote branch with your lasted rebased code
git push origin your_branch --force
// or 
git push origin your_branch -f
```
You must push force to your current branch. Otherwise, git will say that your local branch is not updated and require you to pull the code from the remote. Which is a wrong action and may cause a very bad affect. **YOU MUST PUSH FORCE ONCE REBASED WITH MASTER**

6. Once the PR is merge, checkout the master and get the latest code. Then update your master with upstream master
```
// get latest code from upstream master
git checkout master
git pull upstream master --rebase

git push origin master
```

## Git workflow when a task is waiting for another one
Assuming that you have 2 tasks A and B. B is blocked by A. Meaning that you have wait for A to be merged before doing B. But to reduce the wait time, we will do B while waiting for A to be merged with the following step

(Complete the A task with the workflow described above)

1. Because we need code A to be merge in order to do B. So instead of checkout branch B from master, we now will checkout branch B from branch A
```
git checkout branch_a // you are now at branch_a
git checkout -b branch_b // create
```

2. When branch_a has an update
   1. When branch_a is merge, rebase `branch_b` with `master`
      ```
      git checkout master
      git pull upstream master

      git checkout branch_b
      git rebase master
      ```
   2. When branch_a has an update and not merged. Rebase `branch_b` with `branch_a`
      ```
      git checkout branch_a
      // edit branch a

      git checkout branch_b
      git rebase branch_a
      ```

## Other scenario
1. You are doing a task, you want to update your code with master but don't want to commit the current change

```
git stash // temporary commit and save to stash storage
// now  your codebase is doesn't have the latest change
// let's pull the latest code from master and rebase

git stash pop // get the latest change from temporary storage and apply it to the codebase
```

Other `stash` command
```
git stash list // get the current saved temp commit
git stash clear // clear all saved commit
git stash apply n // apply the nth saved commit
git stash show n // check the nth saved commit
```

2. Completely remove the latest commit
```
git commit ...
git reset --hard HEAD~1
// now your latest commit is completely removed
```

3. Revert the latest commit, which changed the commit state to uncommited. All the files you have changed will stay the same
```
git commit ...
git reset --soft HEAD~1
// edit
git commit ...
```

4. Remove all added files with `git add`
```
git add ...
git reset
```

5. Understanding `HEAD`
- HEAD is a reference to the last commit in the currently check-out branch
- [Read More](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git)

6. Get a specific commit from `branch_a` to `branch_b`

```
git checkout branch_a
git log // checking for the commit hash

git checkout branch_b
git cherry-pick [6_digits_from_the_hash]

// i.e: git cherry-pick 123456
```

7. Completely remove the n-latest commit
```
git commit ...
git reset --hard HEAD~n
// now your latest commit is completely removed
```

8. Revert the n-latest commit, which changed the commit state to uncommited. All the files you have changed will stay the same
```
git commit ...
git reset --soft HEAD~n
// edit
git commit ...
```
8. Change existed branch name
```
git branch -m [old_branch_name] [new_branch_name]

```
9. Change commit message of lastest commit
```
git commit --amend
git push -f // After edited, if the commit was pushed to remote we should run push force
```


9. Get a range of commits from `branch_a` to `branch_b`

```
git checkout branch_a
git log // checking for the commit hash

git checkout branch_b
git cherry-pick [start_6_digits_from_the_hash]...[end_6_digits_from_the_hash]

// i.e: git cherry-pick 123456...999999
// note that, start and end commit must be linear and continouse
```

More trick at: [Oh Shit, Git!?!](https://ohshitgit.com/)