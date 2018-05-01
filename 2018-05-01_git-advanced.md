# Leveling up from basic git flow: tips & challenges
#### _NCEAS Hacky Hour 2018-05-01_

## Data Science Fellows

_Our Git flow:_

- `nceas/datamgmt` is our main repository - we try to keep it functional at all times
- the fellows each have forks :fork_and_knife: off the repository, where we develop functions, etc.
- if we work on multiple groups of tasks at once (for example, several unrelated functions), we use branches :deciduous_tree: to keep pull requests into `nceas/datamgmt` separate
  - we'll often use branches anyway so that the master branch in all the forks reflect `nceas/datamgmt@master`
- usually, at least one other fellow will review any incoming pull requests by commenting on it and suggesting changes
- Travis catches structural errors
- we'll often use this workflow for updating our branch before merging it in:

```
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
