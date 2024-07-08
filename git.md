# Version Control
Version control is a system that records changes to files or sets of files over time so that you can recall specific versions later. It is used to manage and track changes in software development, document editing, and other collaborative projects. Version control systems (VCS) help teams manage changes to code and other project files, enabling collaboration and maintaining a history of modifications.

## Types of Version Control Systems
1. Local Version Control Systems: Store versions of files locally on a developer's computer.
2. Centralized Version Control Systems (CVCS): Use a central server to store all versions of files, with clients accessing and checking out files.
3. Distributed Version Control Systems (DVCS): Each user has a complete copy of the repository, including its full history, allowing for better collaboration and offline work.

## How Git Achieves Version Control

Git is a `distributed version control system (DVCS)` designed to handle everything from small to very large projects with speed and efficiency. Here's how Git achieves version control:

### Key Features of Git
1. `Distributed` Architecture: Every user has a complete copy of the repository, including the full history.
Changes can be committed locally and then pushed to a remote repository.

2. `Commit-Based` Versioning: Git tracks changes through commits, which are snapshots of the project's files at a given point in time. Each commit has a `unique SHA-1 hash`, making it easy to identify and reference.

3. `Branching` and `Merging`: Git allows for easy branching and merging, enabling parallel development.
Branches are lightweight and can be created, merged, and deleted quickly.

4. `Staging` Area (Index): Git has a staging area where changes can be reviewed and modified before committing.
This allows for better control over what changes are included in a commit.

5. Efficient Storage: Git uses a `packfile` system to store data efficiently. Only `changes` (deltas) are stored, not entire files, reducing storage space.

6. Distributed Collaboration: Multiple developers can work on the same project `simultaneously`. Changes can be pulled from remote repositories, and conflicts can be resolved locally.

# Branching
## `git rebase`
`git rebase` is a Git command that allows you to move or combine a series of commits to a new base commit. It helps you to create a cleaner and more linear project history.

### How does rebase work?
Imagine you have two branches: main and feature. The branches look like this:
```plain text
main:    A---B---C---D
               \
feature:        E---F---G

```
To make feature look like it branched off after D instead of B, perform:
```plaintext
git checkout feature
git rebase main
```
The branches now look like this:
```plaintext
main:    A---B---C---D
                       \
feature:                E'---F'---G'

```

### Pros / Cons

Pros: Rebasing makes your commit tree look very clean since everything is in a straight line.

Cons: Rebasing modifies the (apparent) history of the commit tree.

For example, commit C1 can be rebased past C3. It then appears that the work for C1' came after C3 when in reality it was completed beforehand.

## `git rebase -i`
`git cherry-pick` is great when you know which commits you want (and you know their corresponding hashes) .

But what about the situation where you don't know what commits you want? We can use interactive rebasing for this -- it's the best way to review a series of commits you're about to rebase.

If you include this option, git will open up a UI to show you which commits are about to be copied below the target of the rebase. It also shows their commit hashes and messages, which is great for getting a bearing on what's what.

you can do many more things like squashing (combining) commits, amending commit messages, and even editing the commits themselves. You can reorder commits and keep all commits or drop specific ones. When the dialog opens.


## Operators
### Caret `^`
The parents of a commit and it can detach HEAD to the most recent commit.

E.g., HEAD^ is the parent of HEAD.
### Tidle `~`
Say you want to move a lot of levels up in the commit tree. It might be tedious to type ^ several times, so Git also has the tilde (~) operator.The tilde operator (optionally) takes in a trailing number that specifies the number of parents you would like to ascend.

E.g., HEAD~1 is the parent of HEAD, and 
HEAD~3 is the 3rd commits before HEAD. 


## Branch Forcing
You can directly reassign a branch to a commit with the `-f` option. So something like:
```plaintext
git branch -f main HEAD~3
```
moves (by force) the main branch to three parents behind HEAD.

## `git checkout`
1. `git checkout <commit-hash>` allows you to change the HEAD to any commit you specify with a hash.
2. `git checkout <branch>` simply changes to that branch.
3. `git checkout -b <branch>` allows you to create a new branch and enter that branch. You can aslo use this to
change the remote: `git checkout -b <branch> <new-remote>`.

### Another way to set remote tracking on a branch!
Simply use the git branch `-u` option. Running

`git branch -u o/main foo` 

will set the foo branch to track o/main. If foo is currently checked out you can even leave it off:

`git branch -u o/main`

# Reverse Changing
## `git reset`
`git reset` reverses changes by moving a branch reference backwards in time to an older commit. In this sense you can think of it as "rewriting history;" git reset will move a branch backwards as if the commit had never been made in the first place.

## `git revert`
While resetting works great for `local` branches on your own machine, its method of "rewriting history" doesn't work for remote branches that others are using.

In order to reverse changes and share those reversed changes with others(i.e. remotely), we need to use `git revert`. 

# Moving Work Around
## `git cherry-pick`
It's a very straightforward way of saying that you would like to copy a series of commits below your current location (HEAD).

`git cherry-pick <command-hash>`

Let's say the initial state of a tree: is shown below:
```plaintext
main:    A---B---C
              \
feature:       D---E---F
```
We want to cherry-pick commits D and E from feature and apply them to main. Hence, we perform
```plaintext
git checkout main
git cherry-pick <commit-D-hash> <commit-E-hash>
```
The state now is like:
```plaintext
main:    A---B---C---D'---E'
              \
feature:       D---E---F
```

# Remote
## Benefits
First and foremost, remotes serve as a great backup! Local git repositories have the ability to restore files to a previous state, but all that information is stored locally. By having copies of your git repository on other computers, you can lose all your local data and still pick up where you left off.

More importantly, remotes make coding social! Now that a copy of your project is hosted elsewhere, your friends can contribute to your project (or pull in your latest changes) very easily.

## `git clone`
Clone the repo.

## Remote Branch
Remote branches (like the branch origin/main) reflect the state of remote repositories since you last talked to those remotes. They help you understand the difference between your local work and what work is public -- a critical step to take before sharing your work with others.

### Property
Remote branches have the special property that when you check them out, you are put into detached HEAD mode. Git does this on purpose because you can't work on these branches directly; you have to work elsewhere and then share your work with the remote (after which your remote branches will be updated).

To be clear: Remote branches are on your local repository, not on the remote repository.

### What is `origin/`?
Remote branches have their name convention: `<remote name>/<branch name>`.
Hence, if you look at a branch named origin/main, the branch name is main and the name of the remote is origin.

### What will happen if you add a new commit to origin/main?
Assume the remote branch origin/main is like c0 <- c1 (origin/main). If a new commit was added by
```plain text
git checkout origin/main; git commit;
```
The git would put us into detached HEAD mode and then not update o/main when we added a new commit. This is because origin/main will only update when the remote updates.

The tree will be like c0 <- c1 (origin/main) <- c2 (HEAD).

## `git fetch`
### What fetch does
git fetch performs two main steps, and two main steps only. It:

1. downloads the commits that the remote has but are missing from our local repository, and...
2. updates where our remote branches point (for instance, o/main)

`git fetch` essentially brings our local representation of the remote repository into synchronization with what the actual remote repository looks like.

As we said before, remote branches reflect the state of the `remote` repositories since you last talked to those remotes. `git fetch` is the way you talk to these remotes! 

`git fetc`h usually talks to the remote repository through the Internet (via a protocol like http:// or git://).

### What fetch doesn't do
`git fetch`, however, does `not` change anything about your `local` state. It will not update your main branch or change anything about how your file system looks right now.

This is important to understand because a lot of developers think that running git fetch will make their local work reflect the state of the remote. It may download all the necessary data to do that, but it does not actually change any of your local files.

Assume the top branch is the remote branch and local branch on your own machine and the bottom branch is the lastest branch:
```plaintext
c0 <- c1 (main; origin/main)                         c0 <- c1 <- c2 <- c3 (main)
```
After git fetch, it looks like:
```plaintext
c0 <- c1 (main) -- c2 -- c3 (origin/main)     
```
### `<place>` Parameter

You can specify a place with git fetch like in the following command:

`git fetch origins <source> <destination>`.

Here is the only catch though -- `<source>` is now a place on the remote and `<destination>` is a local place to put those commits. It's the exact opposite of git push, and that makes sense since we are transferring data in the opposite direction!

That being said, developers rarely do this in practice.

####  Empty`<source>`?
You can technically specify "nothing" as a valid source for git fetch.

Fetching "nothing" to a place locally actually makes a new branch. 

E.g., `git fetch origin :bugFix` will create a new local branch named bugFix.


## `git pull`
The command combines the workflow of fetching remote changes and then merging them.

Here are some equivalent commands in git:

`git pull origin foo` is equal to:

`git fetch origin foo` + `git merge o/foo`

And...

`git pull origin bar:bugFix` is equal to:

`git fetch origin bar:bugFix` + `git merge bugFix`

See? git pull is really just shorthand for fetch + merge, and all git pull cares about is where the commits ended up (the destination argument that it figures out during fetch).


## `git fakeTeamwork`
The `git fakeTeamwork` alias is a custom command you can define to simulate teamwork scenarios in Git. It automates the creation of branches, making commits, and merging branches to help you practice and understand collaborative workflows.

## `git push`
`git push` is responsible for uploading your changes to a specified remote and updating that remote to incorporate your new commits. Once git push completes, all your friends can then download your work from the remote.
### Remote Rejection!
Why Rejected?

If you work on a large collaborative team it's likely that main is locked and requires some Pull Request process to merge changes. If you commit directly to main locally and try pushing you will be greeted with a message similar to this:

```plaintext
! [remote rejected] main -> main (TF402455: Pushes to this branch are not permitted; you must use a pull request to update this branch.)
```

The remote rejected the push of commits directly to main because of the policy on main requiring pull requests to instead be used.

### `<place>` Parameter
What if you wanted to push commits from the foo branch locally onto the bar branch on remote?

In order to specify both the source and the destination of `<place>`, simply join the two together with a colon:

`git push origin <source>:<destination>`

This is commonly referred to as a colon refspec. Refspec is just a fancy name for a location that git can figure out (like the branch foo or even just HEAD~1).

What if the destination you want to push doesn't exist? No problem! Just give a branch name and git will create the branch on the remote for you.

`git push origin <source>:<new-branchs>`

#### Empty`<source>`?
You can technically specify "nothing" as a valid source for both git push.

What does pushing "nothing" to a remote branch do? It deletes it!

`git push origin :foo`

There, we successfully deleted the foo branch on remote by pushing the concept of "nothing" to it.

