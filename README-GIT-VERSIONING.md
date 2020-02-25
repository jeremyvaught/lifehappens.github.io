# Git branching model

Purpose of this README is to clarify how we are to use Git to manage code and for versioning.

* [Git](#git)
  * [Overview](#overview)
  * [Feature branches](#feature-branches)
    * [Starting feature branch](#starting-feature-branch)
    * [Finishing feature branch](#finishing-feature-branch)
    * [Bugfix branches](#bugfix-branches)
    * [Small updates](#small-updates)
  * [Long-lived feature branches](#long-life-feature-branches)
  * [Release Branches](#release-branches)
    * [Starting release branch](#starting-release-branch)
    * [Finishing release branch](#finishing-release-branch)
  * [Hotfix Branches](#hotfix-branches)
    * [Starting hotfix branch](#starting-hotfix-branch)
    * [Finishing hotfix branch](#finishing-hotfix-branch)
* [Versioning](#versioning)
* [Achnowledgments](#acknowledgments)


## Git

### Overview

The goal is to have a clean Git history while maximizing developer simplicity.

There is one primary branch called `master`. Feature branches are created from `master`, `release` and `bugfix` branches are also.

### Feature branches

Most development should be done in a feature branch. Anything with muliple commits certainly should be in a feature branch. A feature branch should be named by prefixing `feature/` and then the Jira ticket if you are working off of one.

#### Starting feature branch

Create with the following command
```
$ git checkout master
$ git pull
$ git checkout -b feature/LHP3-99-my-feature-branch-name
```
![feature branch with labels](https://lifehappens.github.io/images/git/feature-branch-with-labels.png)

Most of the time a feature branch does not need to be pushed. But if you do push a feature branch, once it is merged please delete it from github.

#### Finishing feature branch

When you finish the feature, merge it into `master` and delete the branch.

The method here uses interactive rebase. This is a powerful tool to clean up commits and keep the history clean. If you need a refresher you should check out [this](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) or google and read a few of the many tutorials.

```
$ git checkout master
$ git pull
$ git checkout feature/my-feature
$ git rebase -i master
$ git checkout master
$ git merge --no-ff feature/my-feature
$ git push
$ git branch -d feature/myfeature
```
(and if you have a branch pushed to github, run `git push origin :feature/my-feature` to delete it from the server)

When you run the line `git rebase -i master` on your feature branch it will show that needs to be pushed and pulled at the same time, it gets all crazy, but that's okay, continue with the steps.

What this looks like in a graphic:

![feature branch rebase and merge](https://lifehappens.github.io/images/git/feature-branch-rebase-and-merge-final.png)

#### Bugfix branches

Bugfix branches work exactly the same a feature branches, but use the word bugfix in the branch name like
```
$ git checkout master
$ git pull
$ git branch -b bugfix/LHP3-100-my-bugfix-branch-name
```

#### Small updates

If you are working on something small that requires a single commit, it is not necessary to put it in a `feature`, `bugfix`, or `hotfix` branch. Simply merge it ot master directly.

```
$ git checkout master
$ git pull
$ git add --all
$ git commit -m 'commit message' --ff-only
$ git push
```

What this looks like in a graphic:

![feature branch rebase ff-only.png](https://lifehappens.github.io/images/git/feature-branch-rebase-final.png)


### Long-lived feature branches

If you have a feature that will be long-lived, in order to not get left behind by numerous changes to `master` resulting in many merge conflicts when you finally merge, you should periodically merge `master` back into your feature branch.
```
$ git checkout master
$ git pull
$ git checkout feature/my-feature
$ XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX get rest of example
```

### Release branches

A release branch is for the purpose of of preparing the code for release. Becasue `master` is active development, a `release` branch is created as a middle point before promoting the code to production.

A `release` branch is temporary, it's not long running. It's created, then prepared for production, tagged, and then deleted.

#### Starting release branch

Started similar to a feature branch, however you may not want to use the latest `master`, in that case, start with a specific commit like in the example. For the versioning guideline, see [here](#versioning).

```
$ git checkout master
$ git pull
$ git checkout -b release/3.0.0 a71b71fa
```

#### Finishing release branch

Now we have prepared this branch for production. We need to tag it and merge the changes and tag back into `master`

```
$ git checkout release/3.0.0
$ git tag v3.0.0
$ git checkout master
$ git merge release/3.0.0
$ git push --tags origin master
$ git branch -d release/3.0.0
$ git push origin :release/3.0.0
```

![release-branch-merge-final](https://lifehappens.github.io/images/git/release-branch-merge-final.png)

### Hotfix branches

Hotfixes of course are issues that need to be fixed in procution before the next release branch is promoted. The lifecycle is very similar to a release branch.

#### Starting hotfix branch

```
$ git checkout master
$ git checkout -b hotfix/3.0.1 3.0.0
```

#### Finishing hotfix branch

Once you fix the problem, tag it, merge it to master, then delete it.

```
$ git checkout hotfix/3.0.1
$ git tag 3.0.1
$ git checkout master
$ git merge hotfix/3.0.1
$ git push --tags origin master
$ git branch -d hotfix/3.0.1
$ git push origin :hotfix/3.0.1
```

Graphic example:

![hotfix-branch-merge-final](https://lifehappens.github.io/images/git/hotfix-branch-merge-final.png)

## Versioning

We use Semantic Versioning as laid out by (semver.org)[https://semver.org/. As of this writing we are on 3.0.0 Beta, and the "3" is because this is the third completely different version of the Platform. However going forward, we'll be able to go to 4 and beyond based on the rules of Semantic Versioning.



## Acknowledgments
 * Once upon a time, [Jeremy](https://github.com/jeremyvaught) was a huge fan of [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/), but over time he became frustrated by the readability of the history. It quickly becomes a mess. [This guy does a greate job explaining it more](https://www.endoflineblog.com/gitflow-considered-harmful). So Jeremy started pondering a better way.
 * He thought and thought and thought and came up with something he liked... then he found someone else had almost the same idea, and they called it [OneFlow](https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow). Read more there for his full idea.
 * Graphics also borrowed from [OneFlow](https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow)