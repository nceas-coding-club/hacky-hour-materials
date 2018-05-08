Git Therapy
===========

#### Leveling up from basic git flow: tips & challenges; _NCEAS Hacky Hour 2018-05-08_


![git explained by XKCD](https://imgs.xkcd.com/comics/git.png)

# Opening discussion

1. Discuss the different uses of git at NCEAS:
   - How do you start a repo: locally from GitHub?
   - What tool do you use: RStudio, CLI, other?
   - Which git workflow each team is using: master, branching, forking, ...)
2. List specific topics or points that are source of problems or frustration

## Data Science Fellows

_Our Git flow:_

- `nceas/datamgmt` is our main repository - we try to keep it functional at all times
- the fellows each have forks :fork_and_knife: off the repository, where we develop functions, etc.
- if we work on multiple groups of tasks at once (for example, several unrelated functions), we use branches :deciduous_tree: to keep pull requests into `nceas/datamgmt` separate
  - we'll often use branches anyway so that the master branch in all the forks reflect `nceas/datamgmt@master`
- usually, at least one other fellow will review any incoming pull requests by commenting on it and suggesting changes
- Travis catches structural errors
- we'll often use this workflow for updating our branch before merging it in:

```bash
git checkout master #switch to your master branch
git pull upstream master #pull all commits from NCEAS/datamgmt that you don't have
git push origin master #push your updated local version of master to your fork on github
git checkout BRANCH_NAME #switch to this branch 
git merge master #merge all commits from master into your branch
git push origin BRANCH_NAME #push your branch to a branch on your fork on github
```

_Some issues/questions:_

- dealing with revert/multiple-file merge conflicts
- soft & hard resets - when are they appropriate?
- best practices of when to comment versus when to commit over
- merging vs rebasing


# Specific topics

## git config file
Check your config file:

```bash
git config --list
```

Wow there is a lot of things listed and it is a little be hard to know what is there by default and what has been customized by you. Another command you could use is:

```bash
git config --global --edit
```

This command aims at letting you curomized important part of your git configuration. For example if you do not like `vim`, you can change your text editor (on Mac, you can also use `nano`): 

```bash
git config --global core.editor vim
```

Cache your GitHub credentials (for 1 hour):

```bash
git config --global credential.helper 'cache --timeout=3600'
```

Want to know more about the available configuration options? How to change the default [text editor](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) used by git? How make git remember your [GitHub credentials](https://help.github.com/articles/caching-your-github-password-in-git/#platform-all)?


## git merge conflicts

First: `git pull = git fetch + git merge`

Second: you did nothing wrong!! Git tries to merge files automatically. When the changes on the same file are far apart, git will figure it out on his own and do the merge automatically. However if changes are overalpping, git will call you to the rescue.

### Few tips to deal with merge conflicts

1. If you **know for sure** what file version you want to keep:

 * keep the remote file: ```git checkout --theirs conflicted_file.txt```
 * keep the local file: ```git checkout --ours conflicted_file.txt```

*=> You still have to* ```git add``` *and* ```git commit``` *after this*

2. If you do not know why there is a conflict:
  Dig into the files, looking for:

```bash
<<<<<<< HEAD
local version (ours)
=======
remote version (theirs)
>>>>>>> [remote version (commit#)]
```

Manually edit the file and save.

*=> You still have to* `git add` *and* `git commit` *after this*

- You can always abort a merge, for example you choose to keep the wrong version

```bash
git merge --abort
```
If you realize you made a mistake once the merge resolved, you can always run to go back to the previous commit:

```bash
git reset --hard
```

- tool: [Meld](http://meldmerge.org/)
	
### Few tips to avoid merge conflicts

- try to use the rebase instead of merge:

```bash
git pull --rebase
```

- pull before commiting: By doing a pull before committing, you can avoid a lot of git conflicts. Your git workflow should therefore be:

```bash
git add
git pull
commit -m "descriptive message"
git pull
git push
```

# References

## Git general introduction

- Jenny Bryan Happy git with R <http://happygitwithr.com/>
- Intro to GitHub and using git from RStudio GUI: <http://ohi-science.org/data-science-training/collaborating.html> 
- Intro to git and Github using the command line: <https://nceas.github.io/oss-lessons/version-control/1-git-basics.html>
- Try git in 15min: <https://try.github.io/levels/1/challenges/1>
- Git terminology: <https://www.atlassian.com/git/glossary/terminology>
- git `rebase` <https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase>

## Git workflows

- Comparing workflow: <https://www.atlassian.com/git/tutorials/comparing-workflows>
- Forking vs branching: <https://stackoverflow.com/questions/3611256/forking-vs-branching-in-github>
- Development workflow: <https://github.com/sevntu-checkstyle/sevntu.checkstyle/wiki/Development-workflow-with-Git:-Fork,-Branching,-Commits,-and-Pull-Request>

## Branches

- Interactive tutorial to learn more about git branches and more <https://learngitbranching.js.org/>

## Undoing things

- Help to decide how to undo your problem: <http://justinhileman.info/article/git-pretty/git-pretty.png>
- Undo almost everything with git <https://blog.github.com/2015-06-08-how-to-undo-almost-anything-with-git/>
- Difference between git reset soft, mixed and hard <https://davidzych.com/difference-between-git-reset-soft-mixed-and-hard/>
- Resetting, Checking Out & Reverting <https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting>
