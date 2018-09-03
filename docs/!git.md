## Commonly used git commands

### First time set up

Configure user name and email. Every commit object holds this information, so you can't make a commit without it.

```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

---

Configure aliases to save time when typing commands:

```bash
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.l 'log --all --decorate --oneline --graph'
$ git config --global alias.unstage 'reset HEAD --'
```

You can find global user configuration settings in `$HOME/.giconfig`

### Make a commit

**Stage** (also know as **index**) acts as a buffer for changes that will go into the next commit. You need to add changes to `stage` before you can commit those changes.

`git add .` - stage all changes in the current directories and all subdirectories.
`git add file.txt` - stage a specific file.

---

`git commit -m "#2443 fix broken assets"`  - commit staged changes with a message
`git commit -am "#2443 fix broken assets"` - automatically stage changed files (this doesn't include newly created files, only the files changed since last commit) and make a commit.

### Unstage

`git reset` - unstage all staged changes.

---

`git reset HEAD -- file.txt` - unstage a specific file.
`git unstage file.txt` - the same command, but using the alias created in the setup section.

### Change the last commit

1. make changes in the working directory that you want to include in the last commit.
2. stage made changes using `git add` command
3. use `git commit --amend -m "some commit message"` to change the last commit.

Note: changing the last commit with `git commit --amend` creates a new commit object with a new hash, which means this command changes commit history. You only want to use this command for fixing unpublished commits, i.e. those commits that you haven't yet pushed to a remote repository and shared with others.

---

If you only want to change message of the last commit:

```bash
$ git commit --amend -m "some commit message"
```

---

If you want to make changes to the last commit, but don't want to change its message:

```bash
$ git add file.txt # stage changes you want to add to the last commit
$ git commit --amend --no-edit # don't edit the message
```

### Create a local branch

`git branch new-feature` - creat a local branch off the current commit. To switch to that branch use `git checkout new-feature`.
`git checkout -b new-feature` - create a local branch named `new-feature` off the current commit and switch to that branch.

### Create a remote branch

```bash
$ git checkout -b new-feature # create a local branch and switch to it
$ git push -u origin new-feature # create a remote branch and tell the local to track it
```
`-u` - tells git that the local `new-feature` (on which you're now) should be _tracking_ branch `new-feature` in remote repository. Tracking means that syncronization commands like `git fetch`, `git push`, `git pull` will work without you having to specify exlicitly to which branch you want to push/download commits.

`origin` is a shortname for a remote repository. Git matches a URL of a remote repository with a shortname, which makes it easy to specify a remote repository without having to remember the whole URL. `origin` is the default name that is given to a remote repository when you do `git clone`. So if you `cloned` your repository, you can simply refer to remote repository by its shortname `origin` in all other commands.

### List branches

`git branch` - list local branches.
`git branch -r` - list remote branches. Remote branches will most likely have prefix `origin/` which indicates the remote repository where the branch exists. Local branches won't have this prefix.
`git branch -a` - list local and remote branches.

### Checkout a remote branch

When you do `git clone` only the master branch is checked out by default. If you want to download other remote branches you'll need to use the following command:

`git brach -r` - find a remote branch you want to checkout
`git checkout new-feature` - checkout a remote branch (note: you don't have to use `origin` prefix).

### Show commit history

`git log` - show commits of the current branch
`git log --all --decorate --oneline --graph` - show commit history of all branches and show the graph of commits. (use alias `git l` from above)

### Undoing commits

Use `git reset` for unpublished commits (i.e., local commits that are not shared with others) as it changes the commit history.

`git reset --hard <commit>` - reset the HEAD (pointer to the current commit) to a specified commit on the branch. Any changes to tracked files in the working directory since `<commit>` are discarded.
`git reset --soft <commit>` - reset the HEAD to a specified commit on the branch. Changed files after `<commit>` won't be removed and will be placed in staging.
`git reset <commit>` - reset the HEAD to a specified commit on the branch. Changed files after `<commit>` won't be removed and but won't be staged as in case of `--soft`.

[Explanation of the difference between --soft, --hard, --mixed options with example](https://gist.github.com/tnguyen14/0827ae6eefdff39e452b)

---

For published commits (pushed to the remote repo), use `git revert`. This will create a new commit, that reverts the changes of the commit you specify.

`git revert <commit>` - revert a commit.
`git revert <commit> <file>` - This turns the <file> that resides in the working directory into an exact copy of the one from <commit> and adds it to the staging area. You can then re-commit the old version as you would any other file. This basically serves as a way to revert back to an old version of an individual file.

### Checkout a commit

If you want to view a state of you files at particular commit, use `git checkout <commit>` to move you HEAD to that commit. The content of your repo will be changed to reflect the state of this particular commit. After you’re finished viewing the old version of your repository, you can move the HEAD back to the tip of your branch and load the current state of your repository:
```
git checkout master
```

### Commit reference

You can always reference a commit by hash, which you can get from `git log` output.

HEAD - current commit.
HEAD~1 - first parent of the current commit.
master~1 - first parent of the commit to which `master` is pointing right now.

### Commit search

[Examples](http://artemstar.com/2017/03/26/git/).

`git log -S "Hello, World!" --oneline` - search by the commit content. Use it when you want to find a commit when a specific line of code was introduced or deleted.
---

`git log --grep="#2244" --oneline` - search by commit message.


### Diff

`git diff <commit>` - show changes in the working directory not staged for commit relative to the specified commit. If no commit is given, it defaults to the latest commit (HEAD). Thus, `git diff` will show unstaged changes in the working directory relative to the last commit.
`git diff --staged <commit>` - show changes staged for commit.
`git diff <commit> -- ./file.txt` - show changes of a specific file not staged for commit. Instead of path to a file, you can also specify path to a directory after `--`.
`git diff <commit1> <commit2>` - show changes between two commits.
























