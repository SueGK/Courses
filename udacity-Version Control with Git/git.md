```console
$ git add -h
usage: git add [<options>] [--] <pathspec>...

    -n, --dry-run         dry run
    -v, --verbose         be verbose

    -i, --interactive     interactive picking
    -p, --patch           select hunks interactively
    -e, --edit            edit current diff and apply
    -f, --force           allow adding otherwise ignored files
    -u, --update          update tracked files
    --renormalize         renormalize EOL of tracked files (implies -u)
    -N, --intent-to-add   record only the fact that the path will be added later
    -A, --all             add changes from all tracked and untracked files
    --ignore-removal      ignore paths removed in the working tree (same as --no-all)
    --refresh             don't add, only refresh the index
    --ignore-errors       just skip files which cannot be added because of errors
    --ignore-missing      check if - even missing - files are ignored in dry run
    --chmod (+|-)x        override the executable bit of the listed files
```

```console
mkdircd() { #Make path for each argument and cd into the last path mkdir -p "$@" && cd "$_" }
```

## git init
Use the `git init` command to create a new, empty repository in the current directory.

### .Git Directory Contents

_We're about to take a look at the `.git` directory...it's not vital for this course, though, so don't worry about memorizing anything, it's here if you want to dig a little deeper into how Git works under the hood._

Here's a brief synopsis on each of the items in the `.git` directory:

-   **config file** - where all _project specific_ configuration settings are stored.  
    From the [Git Book](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration):  
    
    > Git looks for configuration values in the configuration file in the Git directory (.git/config) of whatever repository you’re currently using. These values are specific to that single repository.
    
    For example, let's say you set that the global configuration for Git uses your personal email address. If you want your work email to be used for a specific project rather than your personal email, that change would be added to this file. 
    

-   **description file** - this file is only used by the GitWeb program, so we can ignore it

-   **hooks directory** - this is where we could place client-side or server-side scripts that we can use to hook into Git's different lifecycle events

-   **info directory** - contains the global excludes file

-   **objects directory** - this directory will store all of the commits we make

-   **refs directory** - this directory holds pointers to commits (basically the "branches" and "tags") 

Remember, other than the "hooks" directory, you shouldn't mess with pretty much any of the content in here. The "hooks" directory _can_ be used to hook into different parts or events of Git's workflow, but that's a more advanced topic that we won't be getting into in this course.

### Further Research

-   [Git Internals - Plumbing and Porcelain](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) (advanced - bookmark this and check it out later)
-   [Customizing Git - Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

## git clone
```
$ git clone https://github.com/udacity/course-git-blog-project
```
rename clone repository, you can specify the new directory name as an additional argument:
```console
$ git clone https://github.com/udacity/course-git-blog-project new-name
```
**Verify Terminal Location**
TIP: Now before you clone anything, make sure you are located in the correct directory on the command line. Cloning a project creates a new directory and places the cloned Git repository in it. The problem is that you can't have nested Git repositories. So make sure the terminal's current working directory isn't located in a Git repository. If your current working directory is not in your shell's prompt, type pwd to print the working directory.

### Git Clone Recap

The `git clone` command is used to create an identical copy of an existing repository.

```
$ git clone <path-to-repository-to-clone>
```

This command:

-   takes the path to an existing repository
-   by default will create a directory with the same name as the repository that's being cloned
-   can be given a second argument that will be used as the name of the directory
-   will create the new repository inside of the current working directory

### Helpful Links

-   [Cloning an Existing Repository](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository#Cloning-an-Existing-Repository)
-   [git clone docs](https://git-scm.com/docs/git-clone)
-   [git clone Tutorial](https://www.atlassian.com/git/tutorials/setting-up-a-repository)

## git status

As you can see in the GIF above, running `git status` in the `course-git-blog-project` project produces the following output:

```
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```
 ### Explanation
The output tells us two things:

1.  `On branch master` – this tells us that Git is on the `master` branch. You've got a description of a branch on your terms sheet so this is the "master" branch (which is the default branch). We'll be looking more at branches in lesson 5
2.  `Your branch is up-to-date with 'origin/master'.` – Because `git clone` was used to copy this repository from another computer, this is telling us if our project is in sync with the one we copied from. We won't be dealing with the project on the other computer, so this line can be ignored.
3.  `nothing to commit, working directory clean` – this is saying that there are no pending changes.
### Helpful Links

-   [Checking the Status of Your Files](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#Checking-the-Status-of-Your-Files)
-   [git status docs](https://git-scm.com/docs/git-status)
-   [git status Tutorial](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-status)

## git log
By _default_, this command displays:

-   the SHA
-   the author
-   the date
-   and the message

### Navigating The Log

If you're not used to a pager on the command line, navigating in [Less](https://en.wikipedia.org/wiki/Less_(Unix)) can be a bit odd. Here are some helpful keys:

-   to scroll **down**, press
    -   `j` or  `↓` to move _down_ one line at a time
    -   `d` to move by half the page screen
    -   `f` to move by a whole page screen
-   to scroll **up**, press
    -   `k` or `↑` to move _up_ one line at a time
    -   `u` to move by half the page screen
    -   `b` to move by a whole page screen
-   press `q` to **quit** out of the log (returns to the regular command prompt)

What could we do here to not waste a lot of space and make the output smaller? We can use a **flag**.

Flags can be used to alter how a program functions and/or what is displayed. To learn more about command line programs and flags, check out our course [Linux Command Line Basics](https://www.udacity.com/course/linux-command-line-basics--ud595).

## git log --oneline

The `git log` command has a flag that can be used to alter how it displays the repository's information. That flag is `--oneline`:
![kmISzO](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/kmISzO.png)
This command:
-   lists one commit per line
-   shows the first 7 characters of the commit's SHA
-   shows the commit's message

## git log --stat
-   displays the file(s) that have been modified
-   displays the number of lines that have been added/removed
-   displays a summary line with the total number of modified files and lines that have been added/removed

PREVIOUSNEXT
![CRzpxt](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/CRzpxt.png)

## git log -p

-   displays the files that have been modified
-   displays the location of the lines that have been added/removed
-   displays the actual changes that have been made


The `git log` command has a flag that can be used to display the actual changes made to a file. The flag is `--patch` which can be shortened to just `-p`:

### Annotated `git log -p` Output

Using the image above, let's do a quick recap of the `git log -p` output:
![po7H0E](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/po7H0E.png)
-   🔵 - the file that is being displayed
-   🔶 - the hash of the first version of the file and the hash of the second version of the file
    -   not usually important, so it's safe to ignore
-   ❤️ - the old version and current version of the file 
-   🔍 - the lines where the file is added and how many lines there are
    -   `-15,83` indicates that the old version (represented by the `-`) started at line 15 and that the file had 83 lines
    -   `+15,85` indicates that the current version (represented by the `+`) starts at line 15 and that there are now 85 lines...these 85 lines are shown in the patch below
-   ✏️ - the actual changes made in the commit
    -   lines that are red and start with a minus (`-`) were in the original version of the file but have been removed by the commit
    -   lines that are green and start with a plus (`+`) are new lines that have been added in the commit

### Further Research
-   [Generating patches with -p](https://git-scm.com/docs/git-diff#_generating_patches_with_p) from the Git docs

## git log -p --stat
you can combine flags! `git log -p --stat`will display the stats info above the patch info. Actually, the order doesn't matter; `git log --stat -p` will also show the stats info above the patch info.

## git log -p -w
show the patch information, but will not highlight lines where _only_whitespace changes have occurred.

-w
--ignore-all-space
Ignore whitespace when comparing lines. This ignores differences even if one line has whitespace where the other line has none.

--ignore-blank-lines
Ignore changes whose lines are all blank.


## git log -p SHA / git show SHA
```
$ git log -p fdf5493
```
By supplying a SHA, the `git log -p` command will _start at that commit_! No need to scroll through everything! Keep in mind that it will _also_ show all of the commits that were made _prior_ to the supplied SHA.

`$ git show fdf5493`

The `git show` command will show _only one commit_. So don't get alarmed when you can't find any other commits - it only shows one. The output of the `git show` command is exactly the same as the `git log -p` command. So by default, `git show` displays:

-   the commit
-   the author
-   the date
-   the commit message
-   the patch information

However, `git show` can be combined with most of the other flags we've looked at:

-   `--stat` - to show the how many files were changed and the number of lines that were added/removed
-   `-p` or `--patch` - this the default, but if `--stat` is used, the patch won't display, so pass `-p` to add it again
-   `-w` - to ignore changes to whitespace

## git log --oneline --graph --all
The `--graph` flag adds the bullets and lines to the leftmost part of the output. This shows the actual _branching_ that's happening. The `--all` flag is what displays _all_ of the branches in the repository.

## git add

The `git add` command is used to move files from the Working Directory to the Staging Index.
```
$ git add <file1> <file2> … <fileN>
```
This command:
-   takes a space-separated list of file names
-   alternatively, the period `.` can be used in place of a list of files to tell Git to add the current directory (and all nested files)

> TIP: Did you also notice the helpful text that's located just beneath "Changes to be committed"? It says `(use "git rm --cached <file>..." to unstage)` This is a hint of what you should do if you accidentally ran `git add` and gave it the wrong file.

> As a side note, `git rm --cached` is not like the shell's `rm` command. `git rm --cached` will not destroy any of your work; it just removes it from the Staging Index.

> Also, this used the word "unstage". The act of moving a file from the Working Directory to the Staging Index is called "staging". If a file has been moved, then it has been "staged". Moving a file from the Staging Index _back_to the Working Directory will unstage the file. If you read documentation that says "stage the following files" that means you should use the `git add` command.

### Stage Remaining Files

The `index.html` file has been staged. Let's stage the other two files. Now we _could_ run the following:

```
$ git add css/app.css js/app.js
```

...but that's a lot of extra typing. We could use a special command line character to help:

### The Period `.`

The period refers to the current directory and can be used as a shortcut to refer to all files and directories (including all nested files and directories!).

```
$ git add css/app.css js/app.js
# would become
$ git add .
```

The only thing to be careful of is that you might accidentally include more files than you meant to. Right now we _want_ both `css/app.css` and `js/app.js` to be staged, so running this command is fine right now. But let's say you added some images to an `img` directory but didn't want to stage them just yet. Running `git add .` _will_ stage them. If you do stage files that you didn't mean to, remember that `git status` will tell you the command to use to "unstage" files.

## git commit

The `git commit` command takes files from the Staging Index and saves them in the repository.

```
$ git commit
```

This command:

-   will open the code editor that is specified in your configuration
    -   (check out the Git configuration step from the first lesson to configure your editor)

Inside the code editor:

-   a commit message must be supplied
-   lines that start with a `#` are comments and will not be recorded
-   save the file after adding a commit message
-   close the editor to make the commit

Then, use `git log` to review the commit you just made!

### Further Research

-   [Associating text editors with Git](https://help.github.com/articles/associating-text-editors-with-git/) from GitHub Help Docs
-   [Getting Started - First-Time Git Setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) from Git book

### Bypass The Editor With The `-m` Flag

> TIP: If the commit message you're writing is short and you don't want to wait for your code editor to open up to type it out, you can pass your message directly on the command line with the `-m` flag:
`$ git commit -m "Initial commit"`

### Good commit message
Now, what makes a "good" commit message? That's a great question and has been [written about](https://chris.beams.io/posts/git-commit/) [a number](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c#.jwprsco0n) [of times](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html). Here are some important things to think about when crafting a good commit message:

**Do**

-   do keep the message short (less than 60-ish characters)
-   do explain _what_ the commit does (not _how_ or _why_!)

**Do not**

-   do not explain _why_ the changes are made (more on this below)
-   do not explain _how_ the changes are made (that's what `git log -p` is for!)
-   do not use the word "and"
    -   if you have to use "and", your commit message is probably doing too many changes - break the changes into separate commits
    -   e.g. "make the background color pink _and_ increase the size of the sidebar"

The best way that I've found to come up with a commit message is to finish this phrase, "This commit will...". However, you finish that phrase, use _that_ as your commit message.

Above all, _**be consistent**_ in how you write your commit messages!

### Explain the Why

If you need to explain _why_ a commit needs to be made, you can!

When you're writing the commit message, the first line is the message itself. After the message, leave a blank line, and then type out the body or explanation including details about why the commit is needed (e.g. URL links).

Here's what a commit message edit screen might look like:
![pID4IX](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/pID4IX.png)

## git diff

To recap, the `git diff` command is used to see changes that have been made but haven't been committed, yet:

```
$ git diff
```

This command displays:

-   the files that have been modified
-   the location of the lines that have been added/removed
-   the actual changes that have been made

### Further Research

-   [git diff](https://git-scm.com/docs/git-diff) from the Git Docs

## git ignore

If you want to keep a file in your project's directory structure but make sure it isn't accidentally committed to the project, you can use the specially named file, `.gitignore` (note the dot at the front, it's important!). Add this file to your project in the same directory that the hidden `.git` directory is located. All you have to do is list the _names_ of files that you want Git to ignore (not track) and it will ignore them.

### Further Research

-   [Ignoring files](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#Ignoring-Files) from the Git Book
-   [gitignore](https://git-scm.com/docs/gitignore#_pattern_format) from the Git Docs
-   [Ignoring files](https://help.github.com/articles/ignoring-files/) from the GitHub Docs
-   [gitignore.io](https://www.gitignore.io/)

## Globbing Crash Course

Let's say that you add 50 images to your project, but want Git to ignore all of them. Does this mean you have to list each and every filename in the `.gitignore` file? Oh gosh no, that would be crazy! Instead, you can use a concept called [globbing](https://en.wikipedia.org/wiki/Glob_(programming).

Globbing lets you use special characters to match patterns/characters. In the `.gitignore` file, you can use the following:

-   blank lines can be used for spacing
-   `#` - marks line as a comment
-   `*` - matches 0 or more characters
-   `?` - matches 1 character
-   `[abc]` - matches a, b, _or_ c
-   `**` - matches nested directories - `a/**/z` matches
    -   a/z
    -   a/b/z
    -   a/b/c/z

So if all of the 50 images are JPEG images in the "samples" folder, we could add the following line to `.gitignore` to have Git ignore all 50 images.

```
samples/*.jpg
```

## git tag
### tag the most recent commit
```
$ git tag -a v1.0
```
> CAREFUL: In the command above (`git tag -a v1.0`) the `-a` flag is used. This flag tells Git to create an _annotated_ flag. If you don't provide the flag (i.e. `git tag v1.0`) then it'll create what's called a _lightweight_ tag.

> Annotated tags are recommended because they include a lot of extra information such as:
> 
> -   the person who made the tag
> -   the date the tag was made
> -   a message for the tag


### Adding A Tag To A Past Commit

Running `git tag -a v1.0` will . But what if you wanted to tag a commit that occurred farther back in the repo's history?

All you have to do is provide the SHA of the commit you want to tag!

```
$ git tag -a v1.0 a87984
```

### Further Research

-   [Git Basics - Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging) from the Git Book
-   [Git Tag](https://git-scm.com/docs/git-tag) from the Git Docs

## git tag -d
A Git tag can be deleted with the `-d` flag (for _delete_!) and the name of the tag:

```
$ git tag -d v1.0
```
![QwBmPt](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/QwBmPt.png)



## git branch

It can be used to:

-   list all branch names in the repository
-   create new branches `git branch sidebar`
-   delete branches

If we type out just `git branch` it will list out the branches in a repository

```
git branch alt-sidebar-loc 42a69f
```
It creates a new branch called `alt-sidebar-loc` and has it pointing at the commit with the SHA `42a69f`

Deleting something can be quite nerve-wracking. Don't worry, though. Git won't let you delete a branch if it has commits on it that aren't on any other branch (meaning the commits are unique to the branch that's about to be deleted). If you created the `sidebar` branch, added commits to it, and then tried to delete it with the `git branch -d sidebar`, Git wouldn't let you delete the branch because you can't delete a branch that you're currently on. If you switched to the `master` branch and tried to delete the `sidebar` branch, Git _also_ wouldn't let you do that because those new commits on the `sidebar` branch would be lost! To force deletion, you need to use a capital D flag - `git branch -D sidebar`.

### change branch name 
`$ git branch-m &NEWBRANCHNAME>

## Git Branch Recap

To recap, the `git branch` command is used to manage branches in Git:

```
# to list all branches
$ git branch

# to create a new "footer-fix" branch
$ git branch footer-fix

# to delete the "footer-fix" branch
$ git branch -d footer-fix
```

This command is used to:

-   list out local branches
-   create new branches
-   remove branches

### Further Research

-   [Git Branching - Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) from the Git Docs
-   [Learn Git Branching](http://learngitbranching.js.org/)
-   [Git Branching Tutorial](https://www.atlassian.com/git/tutorials/using-branches) from the Atlassian Blog

PREVIOUSNEXT

## git checkout

It's important to understand how this command works. Running this command will:

-   remove all files and directories from the Working Directory that Git is tracking
    -   (files that Git tracks are stored in the repository, so nothing is lost)
-   go into the repository and pull out all of the files and directories of the commit that the branch points to

So this will remove all of the files that are referenced by commits in the master branch. It will replace them with the files that are referenced by the commits in the sidebar branch.

### git checkout -b
`git checkout` command can actually create a new branch, too? If you provide the `-b` flag, you can create a branch _and_ switch to it all in one command.

## merging

Combining branches together is called **merging**. Pay attention to the two main types of merges in Git, a regular **merge** and a **Fast-forward merge**.
Remember that making a merge makes a commit.

## git merge

```
$ git merge <name-of-branch-to-merge-in>
```

When a merge happens, Git will:

-   look at the branches that it's going to merge
-   look back along the branch's history to find a single commit that _both_ branches have in their commit history
-   combine the lines of code that were changed on the separate branches together
-   makes a commit to record the merge
- When we merge, we're merging some other branch into the current (checked-out) branch. We're not merging two branches into a new branch. We're not merging the current branch into the other branch.

### Fast-forward Merge
Now, since `footer` is directly ahead of `master`, this merge is one of the easiest merges to do. Merging `footer` into `master` will cause a **Fast-forward merge**. A Fast-forward merge will just move the currently checked out branch _forward_ until it points to the same commit that the other branch (in this case, `footer`) is pointing to.

To merge in the `footer` branch, run command in `master` branch:

```
$ git merge footer
```
![T1DGyH](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/T1DGyH.png)

After merge
![wlryRx](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/wlryRx.png)

### Regular merge
```
$ git merge sidebar
```

Because this combines two divergent branches, a commit is going to be made. 
Since this is a _merge commit_ a default message is already supplied. You can change the message if you want, but it's common practice to use the default merge commit message. So when your code editor opens with the message, just close it again and accept that commit message.
![cQGACU](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/cQGACU.png)
![Fo29vZ](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/Fo29vZ.png)

### Merge Recap

To recap, the `git merge` command is used to combine branches in Git:

```
$ git merge <other-branch>
```

There are two types of merges:

-   Fast-forward merge – the branch being merged in must be _ahead_ of the checked out branch. The checked out branch's pointer will just be moved forward to point to the same commit as the other branch.
-   the regular type of merge
    -   two divergent branches are combined
    -   a merge commit is created

### Further Research

-   [Basic Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging#Basic-Merging) from Git Book
-   [git-merge](https://git-scm.com/docs/git-merge) from Git Docs
-   [git merge](https://www.atlassian.com/git/tutorials/git-merge) from Atlassian blog

## merge conflicts

### Merge Conflict Output Explained

The output that shows in the Terminal is:

```
$ git merge heading-update 
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### Merge Conflict Indicators Explanation
![8aGJEi](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/8aGJEi.png)

The editor has the following merge conflict indicators:

-   `<<<<<<< HEAD` everything below this line (until the next indicator) shows you what's on the current branch
-   `||||||| merged common ancestors` everything below this line (until the next indicator) shows you what the original lines were
-   `=======` is the end of the original lines, everything that follows (until the next indicator) is what's on the branch that's being merged in
-   `>>>>>>> heading-update` is the ending indicator of what's on the branch that's being merged in (in this case, the `heading-update` branch)

### Resolving A Merge Conflict

Git is using the merge conflict indicators to show you what lines caused the merge conflict on the two different branches as well as what the original line used to have. So to resolve a merge conflict, you need to:

1.  choose which line(s) to keep
2.  remove all lines with indicators

![Code editor where showing the merge conflict indicators being removed and the page heading being updated.](https://video.udacity-data.com/topher/2017/February/58a74ced_ud123-l5-resolve-merge-conflict/ud123-l5-resolve-merge-conflict.gif)

Once you've removed all lines with merge conflict indicators and have selected what heading you want to use, just save the file, add it to the staging index, and commit it!

### Merge Conflict Recap

A merge conflict happens when the same line or lines have been changed on different branches that are being merged. Git will pause mid-merge telling you that there is a conflict and will tell you in what file or files the conflict occurred. To resolve the conflict in a file:

-   locate and remove all lines with merge conflict indicators
-   determine what to keep
-   save the file(s)
-   stage the file(s)
-   make a commit

Be careful that a file might have merge conflicts in multiple parts of the file, so make sure you check the entire file for merge conflict indicators - a quick search for `<<<` should help you locate all of them.

### Further Research

-   [Basic Merge Conflicts](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging#Basic-Merge-Conflicts) from the Git book
-   [How Conflicts Are Presented](https://git-scm.com/docs/git-merge#_how_conflicts_are_presented) from the Git docs

# Undo changes
## Changing The Last Commit  git commit --amend
If your Working Directory is clean (**meaning there aren't any uncommitted changes in the repository**), then running `git commit --amend` will let you provide a new commit message. Your code editor will open up and display the original commit message. Just fix a misspelling or completely reword it! Then save it and close the editor to lock in the new commit message.

### Add Forgotten Files To Commit
To do get the forgotten link included, just:
-   edit the file(s)
-   save the file(s)
-   stage the file(s)
-   and run `git commit --amend`

## Reverse previous commit git revert  SHA

To recap, the `git revert` command is used to reverse a previously made commit:

```$ git revert  SHA-of-commit-to-revert```

This command:

-   will undo the changes that were made by the provided commit
-   creates a new commit to record the change
![tDT6ft](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/tDT6ft.png)

### Further Research

-   [git-revert](https://git-scm.com/docs/git-revert) from Git Docs
-   [git revert](https://www.atlassian.com/git/tutorials/undoing-changes) Atlassian tutorial


## Reset(erase) commit

> ## ⚠️ Resetting Is Dangerous ⚠️

> You've got to be careful with Git's resetting capabilities. This is one of the few commands that lets you erase commits from the repository. If a commit is no longer in the repository, then its content is gone.

> To alleviate the stress a bit, Git _does_ keep track of everything for about 30 days before it completely erases anything. To access this content, you'll need to use the `git reflog` command. Check out these links for more info:

> -   [git-reflog](https://git-scm.com/docs/git-reflog)
> -   [Rewriting History](https://www.atlassian.com/git/tutorials/rewriting-history)
> -   [reflog, your safety net](http://gitready.com/intermediate/2009/02/09/reflog-your-safety-net.html)
### Relative Commit References
Here's how we can refer to previous commits:
-   `^` – indicates the parent commit
-   `~` – indicates the _first_ parent commit

-   the parent commit – the following indicate the parent commit of the current commit
    -   HEAD^
    -   HEAD~
    -   HEAD~1
-   the grandparent commit – the following indicate the grandparent commit of the current commit
    -   HEAD^^
    -   HEAD~2
-   the great-grandparent commit – the following indicate the great-grandparent commit of the current commit
    -   HEAD^^^
    -   HEAD~3

The main difference between the `^` and the `~` is when a commit is created _from a merge_. A merge commit has _two_ parents. With a merge commit, the `^` reference is used to indicate the _first_ parent of the commit while `^2` indicates the _second_ parent. The first parent is the branch you were on when you ran `git merge` while the second parent is the branch that was merged in.

![4pUk4r](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/4pUk4r.png)

which commit is referenced by `HEAD~4^2`? ---f69811c
Which commit is referenced by HEAD~6? ---209752a

## git reset

The `git reset` command is used to reset (erase) commits:

```
$ git reset <reference-to-commit>
```

It can be used to:
-   move the HEAD and current branch pointer to the referenced commit
-   erase commits
-   move committed changes to the staging index
-   unstage committed changes

### Git Reset's Flags

The way that Git determines if it erases, stages previously committed changes, or unstages previously committed changes is by the flag that's used. The flags are:
-   `--mixed`
-   `--soft`
-   `--hard`

![OfXQE4](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/OfXQE4.png)
![Hs0Ox0](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/Hs0Ox0.png)

> ## 💡 Backup Branch 💡

> Remember that using the `git reset` command will _erase_ commits from the current branch. So if you want to follow along with all the resetting stuff that's coming up, you'll need to create a branch on the current commit that you can use as a backup.

> Before I do any resetting, I usually create a `backup` branch on the most-recent commit so that I can get back to the commits if I make a mistake:

> ```
> $ git branch backup
> ```

### Reset's `--mixed` Flag

Let's look at each one of these flags.

```
* 9ec05ca (HEAD -> master) Revert "Set page heading to "Quests & Crusades""
* db7e87a Set page heading to "Quests & Crusades"
* 796ddb0 Merge branch 'heading-update'
```

Using the sample repo above with `HEAD` pointing to `master` on commit `9ec05ca`, running `git reset --mixed HEAD^` will take the changes made in commit `9ec05ca` and move them to the working directory.

![The Terminal application showing the result of resetting with the `--mixed` flag. The changes are unstaged.](https://video.udacity-data.com/topher/2017/February/58a4e242_ud123-l6-git-revert-mixed/ud123-l6-git-revert-mixed.png)

_The Terminal application showing the result of resetting with the `--mixed` flag. The changes are unstaged._

> ## 💡 Back To Normal 💡

> If you created the `backup` branch prior to resetting anything, then you can easily get back to having the `master` branch point to the same commit as the `backup` branch. You'll just need to:
> 
> 1.  remove the uncommitted changes from the working directory
> 2.  merge `backup` into `master` (which will cause a Fast-forward merge and move `master` up to the same point as `backup`)

> ```
> $ git checkout -- index.html
> $ git merge backup
> ```

### Reset's `--soft` Flag

Let's use the same few commits and look at how the `--soft` flag works:

```
* 9ec05ca (HEAD -> master) Revert "Set page heading to "Quests & Crusades""
* db7e87a Set page heading to "Quests & Crusades"
* 796ddb0 Merge branch 'heading-update'
```

Running `git reset --soft HEAD^` will take the changes made in commit `9ec05ca` and move them directly to the Staging Index.

![The Terminal application showing the result of resetting with the `--soft` flag. The changes are moved to the Staging Index.](https://video.udacity-data.com/topher/2017/February/58a4e29a_ud123-l6-git-revert-soft/ud123-l6-git-revert-soft.png)

_The Terminal application showing the result of resetting with the `--soft` flag. The changes are moved to the Staging Index._

### Reset's `--hard` Flag

Last but not least, let's look at the `--hard` flag:

```
* 9ec05ca (HEAD -> master) Revert "Set page heading to "Quests & Crusades""
* db7e87a Set page heading to "Quests & Crusades"
* 796ddb0 Merge branch 'heading-update'
```

Running `git reset --hard HEAD^` will take the changes made in commit `9ec05ca` and erases them.

![The Terminal application showing the result of resetting with the `--hard` flag. The changes are moved erased.](https://video.udacity-data.com/topher/2017/February/58a4e2c2_ud123-l6-git-revert-hard/ud123-l6-git-revert-hard.png)

_The Terminal application showing the result of resetting with the `--hard` flag. The changes are moved erased._

### Further Research

-   [git-reset](https://git-scm.com/docs/git-reset) from Git docs
-   [Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) from Git Blog
-   [Ancestry References](https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection#Ancestry-References) from Git Book

## Expand On Your Git Skills

-   take the companion GitHub course
-   create a repo to track your computer's settings - [https://dotfiles.github.io/](https://dotfiles.github.io/)
-   develop the next, awesome feature for your personal project
-   try tackling some Git challenges with the [Git-it app](https://github.com/jlord/git-it-electron)
- [Udacity Nanodegree Style Guide](https://udacity.github.io/git-styleguide/)


# Remote Control
## Add remote connections
`$ git remote add ＜REMOTENAME＞ ＜URL> `
## Set a URL to a remote
`$ git remote set-urL <REMOTENAME> <URL>`

## Pull in changes
`$ git pull ＜REMOTENAME> ＜BRANCHNAME>`
## View remote addresses
`$ git remote-v`
## Push changes
`git push ＜REMOTENAME> ＜BRANCH>`

## Delete a remote branch
`$ git push <REMOTENAME>--delete <BRANCHNAME>`

## fork and clone
To be sure you have the correct remotes set up, type git remote-v to list out the
addresses you have stored. You should have an 'origin' remote with your fork's
address and then an 'upstream' remote with the address to the original, the URL
noted above in this step.
`$ git remote add upstream https://github.com/jlord/patchwork.git`

Add a remote
`$ git remote add ＜REMOTENAME> ＜URL>`

Change a remote URL
`$ git remote set-url ＜REMOTENAME> ＜URL>`

View remote connections
`$ git remote-V`

Pullin changes from a remote branch
`$ git pull <REMOTENAME> <REMOTEBRANCH>`

See changes to the remote before you pullin
`$ git fetch--dry-run`
