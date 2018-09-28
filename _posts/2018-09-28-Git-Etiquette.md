---
layout:     post
title:      "Git Etiquette"
subtitle:   " \"Why and how to use Rebase\""
date:       2018-09-28 15:43:00
author:     "Karth"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - Git
    - Software Engineer
    - Version Control
---

# Git Etiquette - How to keep the Git history Clean

## Why is it important to keep the histories clean?

- It is beneficial to use sane git history practices to ensure that when things need to get merged or when something needs to get reverted.
- It is easy to pick out a sensible change set without having to do crazy cherry picking. 
- We use `git rebase` to accomplish this.

## When should you rebase?
- You should rebase when you:
    1. Periodically, while you are working on a branch.
    2. Prior to submitting a pull request. You should rebase your branch and resolve any merge conflicts.
    3. Prior to merging a PR into the development (master) branch. 

## How do you rebase?

Assuming you are working on a branch `feature/CoolStuff` that is based off your `master` branch.
1. If you have any pending work, **Stash any changes you have** by doing the following:
    ```
    $ git add .
    $ git stash
    ```
2. Get latest from `master` 
    ```
    $ git checkout master
    $ git pull --rebase
    ```
3. Get latest from your branch
    ```
    $ git checkout feature/CoolStuff
    $ git pull --rebase
    ```
4. Rebase off the master branch
    ```
    # The -p flag is there to preserve merge conflicts
    $ git rebase master -p
    ```

5. Resolve any merge conflicts
    - **How to resolve conflicts?** You can do so by using build-in Version Control System tools in your IDE or use your terminal to check the files with merge conflcits.
    - **Continuing** Run `git rebase --continue` to continue onto next commit. Repeat until all commits are been fully rebased. 
    - **IF SOMETHING GOES WRONG** Say if you make a terrible mistake, don't panic. Simply run `git rebase --abort` to start over the rebasing process.

6. This may be the scary part. **Use the force.** You should see something like below if you did everything correctly.
    ```
    $ git status
                
    On branch feature/CoolStuff
    Your branch and 'origin/feature-/CoolStuff' have diverged,
    and have 58 and 2 different commits each, respectively.
    (use "git pull" to merge the remote branch into yours)
    nothing to commit, working directory clean
    ```

    Now, simply do a `git push --force` to force push your commit to finish the rebasing process.
    Note: **Do NOT do git pull as suggested in the message**

7. Verify it worked by running `git status`

8. If you have stashed changes from step 1, unstash it by running `git stash pop` 

## Rules of Rebasing
1. You should NEVER rebase `master` branch.
2. Use `git pull --rebase` instead of just `git pull` if you have committed changes.
3. Use `git rebase` requires a certain amount of confidence and comfort with command line terminals. Do not attempt this with a Git GUI unless you are 100% confident that you know what you are doing.
4. `git rebase` involves force push. This may look scary, but it's okay.