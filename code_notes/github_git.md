#Github

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