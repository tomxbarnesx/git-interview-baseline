# Git Collaboration

## Objectives

1. Make a new branch for your repository with `git branch`.
2. Checkout a branch with `git checkout`.
3. Create and checkout a new branch with `git checkout -b`.
4. Create commits within a branch.
5. Merge branches with `git merge`.
6. Update branches from remotes with `git fetch`.
7. Merge updated remote branches with `git merge`.
8. Update and merge remote branches with `git pull`.

## Overview

When our apps start becoming more complicated, or have larger teams working on them, we'll need a tool to help us manage the mounting layers of edits. Enter git, the most popular system for version control out there, available completely open source. Using git, we can add features incrementally, without the risk of breaking our entire app, and capture snapshots of our project at different points in time. 

The key to collaborating with git is to keep discrete and individual lines of work isolated from each other. Consider the following scenario.

You start work on a big feature. You work on it for days, making a few commits that don't entirely finish the feature. Your git log might look like:

```
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

As of two hours ago, it's almost there, but if we had to push it now and deploy the latest version of our code, our users would see a half-finished, currently broken new feature. That's fine if we have the time, but what if we notice a big bug breaking the application for all users? It's an easy fix, and one simple change could make everything work again. Unfortunately, even if you made that commit, you can't deploy. Your edit might fix the bug, but you'd still be pushing a half-finished feature.

```
r4212d1 Fix to application breaking bug (aviflombaum, just now)
512bec5 Still broken, working on new-feature (aviflombaum, 2 hours ago)
62d840 Almost done with new-feature (aviflombaum, 1 day ago)
fbee832 Started new-feature (aviflombaum, 2 days ago)
```

Wouldn't it be great if we simply isolated our work on new-feature into its own copy of our code so that until it's done, we can still easily edit and redeploy our main app? We can do exactly this in git using branches.

## Making a branch with `git branch`

Let's quickly make a repository that we can use as a sandbox to experiment with the collaborative features of git. We recommend following along by trying out some of the commands in your terminal as you read. The best way to get comfortable with git is to experiment, add, commit, push and repeat until the process is second nature. 

Feel free to copy and paste the lines below and pay close attention to any error messages you recieve.

From our home directory we're going to make a new directory for our mission-critical-application.

```
~ $ mkdir mission-critical-application
~ $ cd mission-critical-application
mission-critical-application $ git init
mission-critical-application $ touch application.rb
mission-critical-application $ git add application.rb
mission-critical-application $ git commit -m "First working version of application.rb"
```

Let's break this down piece by piece:

1. First, we made a new directory with `mkdir mission-critical-application`.
2. We moved into that directory with `cd mission-critical-application`.
3. We turned that directory into a git repository with `git init`.
4. We created our application `touch application.rb`.
5. We programmed an entire working first version in `application.rb` (*not reflected in the CLI commands above, but we did, and it was awesome, great job*).
6. We added our `application.rb` to git with `git add application.rb`.
7. We committed the first working version of our application with `git commit -m "First working version of application.rb"`.
8. You deploy your application to production and people start using it (*also not reflected in the CLI commands above, but we did, and it too was awesome, great job*).

Now, let's fast forward a week or two. Our application is online and customers rolling in. But we notice a bug and quickly add a fix in the form of a file, `first-bug-fix.rb`. Back to the command line:

```
mission-critical-application $ touch first-bug-fix.rb
mission-critical-application $ git add first-bug-fix.rb
mission-critical-application $ git commit -m "First bug fix"
```

Right now our git log could be visualized as a timeline composed of two commits.

![First Two Commits](https://dl.dropboxusercontent.com/s/ikorf1qvvp4tay0/2015-11-02%20at%2011.15%20AM.png)

### About the `master` branch.

Notice that these commits are occurring in a linear sequence of events, almost like a timeline. We call this timeline a branch. Whenever you are working on commits in git, you are adding them to branch, and your main default branch at the start of any repository is called the `master`.

![Master Branch](https://dl.dropboxusercontent.com/s/v75as2cf6xr8n8a/2015-11-02%20at%2011.17%20AM.png)

`git status` will always tell you what branch you are on.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
```

One of the most responsible ways to use git is to make sure that the `master` branch is always clean with working code. That way, as in the examples above, if we ever need to add a bug fix, we make the change and deploy a new version of the application immediately. What does that workflow look like?

### Starting a new feature with `git branch new-feature`

To keep `master` clean, when we want to start a new feature, we should do it in an isolated branch. Our timeline will look like this:

![Feature Branch](https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

After commit 2, we're off `master` and working in a completely seperate new timeline. The master timeline remains unchanged and can we revert to it at any time.

To make a new branch simply type: `git branch <branch name>`. 

In our case, for mission-critical-application's new feature, we'll simply name the branch `new-feature` like so:

```
mission-critical-application $ git branch new-feature
```

To see a list of our branches we can type: `git branch -a`

```
mission-critical-application $ git branch -a
* master
  new-feature
```

The `*` in front of the branch `master` indicates that `master` is currently our working branch. We created the `new-feature` branch, but we haven't actually switched to it yet. If we made a commit right now, it would be applied to `master` — a dangerous place to be if we're writing experimental new features.

### Switching branches with `git checkout`

We need to checkout, or move into, our `new-feature` branch so git knows to apply our commits only that isolated timeline. Move between branches with `git checkout <branch name>`.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
mission-critical-application $ git status
On branch new-feature
nothing to commit, working directory clean
```

Breaking this down: we started on `master` and checked out to our `new-feature` branch with `git checkout new-feature`. Now we're ready to commit fresh code.

Let's get this feature started by making a new file, `new-feature-file` to represent our new changes.

```
mission-critical-application $ touch new-feature-file
mission-critical-application $ git add new-feature-file
mission-critical-application $ git commit -m "Started new feature"
[new-feature 332a618] Started new feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature-file
```

You can see the commit we made was in the context of the `new-feature` branch, that 332a618 is the commit's specific id, if we ever need to refer back to it.

Right as we're gettig started, we find another bug and have to move back into `master` to fix and deploy. How do we move from `new-feature` branch back to `master`? And what will our code look like when we're there?

**Protip: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create branch `new-branch-name` and move into it.**

#### Moving back to `master` with `git checkout master`

You can always move between branches with `git checkout`. Since we're currently on `new-feature`, we can move back to master with `git checkout master`.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

And we could move back to `new-feature` with `git checkout new-feature`

```
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

And back again:

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

Notice anything about your files?

![Switching between branches](https://dl.dropboxusercontent.com/s/qzajqsd9f6njauc/2015-11-02%20at%2012.12%20PM.png)

From `master`, you'll see all your changes, namely your new file `new-feature-file`, is gone from the directory.

```
mission-critical-application $ ls
application.rb first-bug-fix.rb
```

The `master` branch only has the code from the most recent commit we made there. The code from our `new-feature` is tucked away in its own branch, waiting patiently for us to return.

Once you're on `master` you're free to make a commit to fix the bug, which we'll represent with a new file, `second-bug-fix.rb`.

```
mission-critical-application $ touch second-bug-fix.rb
mission-critical-application $ git add second-bug-fix.rb
mission-critical-application $ git commit -m "Second bug fix"
```

Let's look at our timeline now.

![Commit on Master](https://dl.dropboxusercontent.com/s/9ipgkog7yv8hrok/2015-11-02%20at%2012.18%20PM.png)

We were able to update the timeline in master with the bug fix without touching any of the code in `new-feature`. You could describe `master` as being 1 commit ahead of the `new-feature` branch.

Let's go back into `new-feature`, complete it, commit it, and then look at our timeline. Remember how to move from `master` back to `new-feature`?

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
mission-critical-application $ git checkout new-feature
Switched to branch 'new-feature'
```

Let's rename `new-feature-file` to `new-feature` to signify the code we wrote to complete the feature. We can rename a file with the `mv <original filename> <new filename>` BASH command.

```
mission-critical-application $ mv new-feature-file new-feature
mission-critical-application $ git add new-feature
mission-critical-application $ git commit -m "Finished feature"
[new-feature bfe50fc] Finished feature
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Our timeline has grown.

![Completed Feature Branch](https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

The final step of our `new-feature` work sprint is to now merge this timeline into the master so everything works together.

## Merging branches with `git merge`

Our goal is to bring the timeline of commits that occurred on the `new-feature` branch into the `master` so that at the end our `master` looks like this:

![Merged Timeline](https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

Once this is complete, `master` will have all of the commits from the `new-feature` branch, as if those events were written straight into the `master` from the beginning.

When we merge a branch with `git merge`, it's important to be on your target branch, the branch you where you want to add the changes. In this case that is the `master` because where we want our commits to end up for deployment. Let's checkout there.

```
mission-critical-application $ git checkout master
Switched to branch 'master'
```

Once on your target branch, type: `git merge <branch name>` where `<branch name>` is the branch we want to merge. In this case, `git merge new-feature` will do the trick.

```
mission-critical-application $ git merge new-feature
Updating e5830af..bfe50fc
Fast-forward
 new-feature      | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 new-feature
```

Wow. Easy. The branches have been merged and if you list your files with `ls`, you'll see the `new-feature` file from in your current working directory. Our new features is no longer isolated on its lonely branch. It's now an integral part of our growing application.

## Working with remote branches with `git fetch` and `git pull`

Git wouldn't be the programming game-changer it is, though, if all your branches stayed confined to your local machine. The real power of git is unlocked when you link your local branches to remote branches that live on the internet, generally GitHub. Pushing your local branches there can allow entire teams to work on a single project, with everyone creating branches, commiting, and downloading and merging these branches.

Whenever you want to update your local copy with all the branches that might have been added to the GitHub remote, you can type `git fetch`.

```
mission-critical-application $ git fetch
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 3), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (4/4), done.
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

It doesn't matter what branch you're in when executing `git fetch`. The result is always the same. The last 3 lines of output are really important, let's take a closer look:

```
From github.com:aviflombaum/mission-critical-application
   bfe50fc..0ae1da2  master     -> origin/master
 * [new branch]      remote-feature-branch -> origin/remote-feature-branch
```

The first line, `From github.com:aviflombaum/mission-critical-application` is informing us which remote our `git fetch` updated from, namely, the remote repository located at: https://github.com/aviflombaum/mission-critical-application.

When we `fetch` with git, we are asking to copy all changes on the remote to our local git repository, but not actually integrate any. The next line, `bfe50fc..0ae1da2  master     -> origin/master` tells us that a new commit was found in `origin/master`, which is simply the GitHub version of `master`. Even though git fetched a new commit from `origin/master`, it did not merge it into the local `master`.

![Fetch without integration](https://dl.dropboxusercontent.com/s/iy2jovft8ykrxbd/2015-11-02%20at%202.08%20PM.png)

Our remote copy on GitHub has a file, `remote-bug-fix`, presumably some code that another developer pushed up to our remote `master` branch to fix a bug. Even after we fetched, our local copy still doesn't appear to have that file.

After you fetch, you have access to the remote code but you still have to merge it. To do that, checkout to your local `master`, type: `git merge origin/master`, referring to the remote branch's full path, `remote/branch`, or `origin/master`.

```
mission-critical-application $ git merge origin/master
Updating bfe50fc..0ae1da2
Fast-forward
 remote-bug-fix | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 remote-bug-fix
mission-critical-application $ ls
 application.rb		new-feature-file    new-feature		
 remote-bug-fix
```

Now `ls` reveals that `remote-bug-fix` is integrated into our local copy of `master` as well. Another great success.

When we fetched, git also outputted: `* [new branch]      remote-feature-branch -> origin/remote-feature-branch`. We fetched an entire new branch, and if we want we can use `git checkout` or `git merge` to start working with it. Let's check what's on `remote-feature-branch`, which some other developer pushed up to GitHub so they could share it with us.

```
mission-critical-application $ git checkout remote-feature-branch
Branch remote-feature-branch set up to track remote branch remote-feature-branch from origin.
Switched to a new branch 'remote-feature-branch'
```

When we checkout a remote branch that was fetched, git will create a local version to track that remote, and then switch to it. We can now write new code, push it back up to GitHub, and another developer can fetch those changes down.

### Combining `git fetch` with `git merge` by using `git pull`

Fetch and pull will get the job done, but you know developers crave effeciency. An easier and more common way to `fetch` and `merge` from GitHub is using `git pull`. Similar to `git checkout -b <branch_name>`, which allows us to create a branch and switch to it in a single motion, `git pull` allows to fetch and merge in one command. It's likely what you'll use to draw new versions down from GitHub 99% of the time.

When you `git pull` the following things will occur:

1. You will `git fetch` all the remote changes, including those on the current branch, existing branches, and new branches.
2. Any changes on a remote branch tracked by your local branch — such as your `master` and `remote/master` — will be automatically merged.

## Conclusion

Git is complex. Used correctly, it can be a saving grace — used carelessly, a source of endless version conflict nightmares. So take your time getting comfortable with it. There's no easy way to have hundreds of people work on the same code base without issue. Master a bit at a time, and eventually its logic will come almost as naturally as saving text documents or sending emails. Almost.

![XKCD Git](http://imgs.xkcd.com/comics/git.png)

<a href='https://learn.co/lessons/git-collaboration-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>

<p class='util--hide'>View <a href='https://learn.co/lessons/git-collaboration-readme'>Git Collaboration</a> on Learn.co and start learning to code for free.</p>
