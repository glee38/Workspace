#Github

##Git Commands
###Adding files to a Git repository
**Ways to add a file to an existing Git repository**:

```
# create new file
$ touch README

# add file to staging area
$ git add README

# commit the file
$ git commit -m 'yada yada'
```

A faster/easier way is to skip the Git staging area:

```
$ touch README
$ git commit -a -m 'yada yada'
```

##What do Git and Github do?
Version control, like Git, provides programmers with the ability to work on isolated copies of code called "versions" or "branches", so that we don't step on each other's toes. We can weave these threads together seamlessly by merging "commits". 

##Forking

Forking means that you're creating your own copy of the lab so that you can solve it.

## Cloning

Cloning is the process of making a copy of the lab from your fork on GitHub to your computer, after which the fork is referred to as the "Origin", since the fork is where the code on your computer originated.

**When you're ready to clone a lab from GitHub, make sure you've first clicked on the SSH link, then click the copy button next to the Clone URL to copy it to your clipboard.**

Next, you'll navigate in Terminal to the parent directory where you would like to place this lab. Then you will type: git clone < paste the clone URL here >

Note: You will replace the < paste the clone URL here > including the < and > symbols in the snippet above with your actual clone URL by pressing command+v on mac or ctrl+v on windows.

+ Example: git clone git@github.com:jongrover/first-lab-000.git

Don't forget to cd into the lab directory that was made when you cloned the lab. Once in the directory, you'll open the directory with your favorite text editor.

##Staging
When you "Stage" a change, you're not making a new version yet, you're simply telling Git, "Hey, this change is going to be included in the next version commit". Any change you don't tell git about will still exist, it just won't be considered a new version to git and everyone else on your team until you stage it and commit it.

+ When you are ready to stage changes for a commit when working on a Lab use **git add**.

+ To stage all changes for a commit, you can type **git add .**, which means "Hey, Git, please stage all the new changes in the current directory."

+ **git status** : checks the git status of that directory

##Commit

Now that all the changes are staged, when you want to create a new commit, a new version of the code with your changes, you can use **git commit**.

When we make a commit, we need to provide Git with a message describing the work done. Since you've solved a lab, "Solution" is a great commit message. When you want to commit all changes with a commit message use **git commit -am "Message"**.

+ ex. **git commit -am "My first commit"**

##Pushing

With your commit created, you now have to upload your local code and commits to your fork/copy of the lab, "Origin", on GitHub.

When you are ready to push a Lab's solution to GitHub, use **git push** command. **git push** instructs git to take your local changes and upload them to the origin remote, which is always your fork, on your GitHub account.

You can think of **git push** as the opposite of **git clone**.

##Pull Request

A Pull Request is a feature of GitHub that allows developers to collaborate on code safely.

A Pull Request on GitHub informs the owners and maintainers of the official project that you want to submit some of your code changes in the form of commits to the official project.

**Here's how to open a Pull Request from GitHub to submit your solution to a lab you've forked and have pushed your solution commit to:**

+ You will open your fork by clicking "View" in GitHub from Learn or just opening your forked repo on GitHub directly. From your forked repository on GitHub, you will click the green Pull Request Button.

+ GitHub will prompt you to review the Pull Request before submitting and tell you to "Create Pull Request". Click the green "Create Pull Request" button on the review page. You do not need to review anything on this page and can skip it entirely by just immediately clicking the "Create Pull Request" button.

+ GitHub will then again prompt you to describe the Pull Request and you can just skip this and again click the green "Create Pull Request" button.

##Github Basics

+ To make a new git repository out of a directory, something you only ever do once per project, you use `git init`. Be careful about making an entire directory, like your home directory or your desktop, into a git repository accidentally. Make sure you only type `git init` within the directory you want to be considered a repository.

+ Whenever you make a change to a file or create a new file, you have to tell git to keep track of that change by staging it via the `git add` command. To capture all changes in a directory, the common pattern, type `git add .` where the `.` refers to the entire current directory.

+ Once your changes have been added and staged to be committed, you can make a commit with the `git commit` command. To capture all changes in a commit, the common pattern, type `git commit -am "Your commit message"` where the `-a` refers to `'all the changes'` and `-m` (combined, `-am`), tells git about the commit message, `"Your commit message"`.

+ To check the status of a repository use `git status`.

##Git Collaboration

###Master Branch

`git status` will always tell you what branch you are on.

```
mission-critical-application $ git status
On branch master
nothing to commit, working directory clean
```

The `master` git branch is our default branch. One of the responsible ways to use git is to make sure that the `master` branch is always clean with working code so that if we ever need to add a bug fix, we can do it and deploy a new version of the application immediately. We don't put broken code in master so that we can always deploy `master`.
###STARTING A NEW FEATURE WITH GIT BRANCH NEW-FEATURE

To keep master clean, when we want to start a new feature, we should do it in an isolated feature branch. Our timeline will look as follows:

![branch example] (https://dl.dropboxusercontent.com/s/d61r0fxyriaf5oj/2015-11-02%20at%2011.52%20AM.png)

**Feature Branch**

After commit 2, we will branch out of master and create a new timeline for commits and events specifically related to the new feature. The master timeline remains unchanged and clean. Now that we've covered the idea of the new-feature branch, let's actually make it.

To make a new branch simply type: `git branch <branch name>`. In the case of a branch relating to a new feature, we'd name the branch `new-feature` like so:

```
mission-critical-application $ git branch new-feature
```

To see a list of our branches we can type: `git branch -a`

```
mission-critical-application $ git branch -a
* master
  new-feature
```

The `*` in front of the branch master indicates that master is our currently working branch and git tells us that we also have a branch called `new-feature`. If we made a commit right now, that commit would still be applied to our master branch.

###SWITCHING BRANCHES WITH GIT CHECKOUT

We need to checkout or move into our `new-feature` timeline or branch so that git knows that all commits made apply to only that unit of work, timeline, or branch. We can move between branches with `git checkout <branch name>`.

**Protip**: You can create and checkout a new branch in one command using: `git checkout -b new-branch-name`. That will both create the branch `new-branch-name` and move into it by checking it out.

###MERGING BRANCHES

Let's look at our timeline now.

![pre-merge branch] (https://dl.dropboxusercontent.com/s/xtoehu7tv5zim6v/2015-11-02%20at%2012.31%20PM.png)

**Completed Feature Branch**

The final step of our `new-feature` work sprint is to figure out how to merge that timeline into the master timeline.

**Merging branches with git merge**

Our goal is to bring the timeline of commits that occurred on the `new-feature` branch into the `master` so that at the end of the operation, our `master` timeline looks like:

![merged example] (https://dl.dropboxusercontent.com/s/bf0cktf3ag549z2/2015-11-02%20at%201.15%20PM.png)

By merging the timelines, `master` will have all of the commits from the `new-feature` branch as though those events occured on the `master` timeline.

When we merge a branch with `git merge`, it's important to be currently working on your target branch, the branch you want to move into. 

Once on your target branch, type: `git merge <branch name>` where `<branch name>` is the branch we want to merge, in this case, `git merge new-feature` will do the trick.

###COMBINING GIT FETCH WITH GIT MERGE BY USING GIT PULL

If you want to both fetch and merge, which is what you want to do 99% of the time, just type `git pull`. `git pull` is literally the combination of both `git fetch` and `git merge`.

When you `git pull` the following things will occur:

+ You will `git fetch` all remote changes, including those on the current branch, existing branches, and new branches.

+ Any changes that are on a remote branch that is being tracked by your local branch, that is to say, if you are on `master` and there is a change to `origin/master`, those changes will be automatically merged.