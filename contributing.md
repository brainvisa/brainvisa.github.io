---
title: BrainVISA contributor's guide
---

Developing and contribnuting to BrainVisa projects using git


General workflow
----------------

Many components of BrainVISA now have their source managed with Git, and are hosted on Github. If you are new to Git, you should read the short [GitHub Git handbook](https://guides.github.com/introduction/git-handbook/). You can find more information in the [Git and GitHub learning resources](https://help.github.com/articles/git-and-github-learning-resources/).

BrainVISA projects are managed by [brainvisa-cmake](http://brainvisa.info/brainvisa-cmake/index.html), a tool to manage the whole development process, including sources management, configuration and build, tests, and more. The main tool of `brainvisa-cmake` is [bv_maker](http://brainvisa.info/brainvisa-cmake/bv_maker.html).

For sources management, `bv_maker` relies on standard source versioning control software, namely `subversion` or `git`. It mainly adds a multi-project management layer.

### Setup

* Get [brainvisa-cmake](http://brainvisa.info/brainvisa-cmake/index.html)
* [configure sources](http://brainvisa.info/brainvisa-cmake/compile_existing.html#edit-bv-maker-configuration-file)

### Read-only update access

* [bv_maker sources](#bv_maker_sources) etc

### Modifying / developing

* [fork the project](forking)
* checkout / create a feature branch
  (for example _soma-io_):

            cd <sources>/soma/soma-io/master
            git checkout -b mybranch

* modify code
* commit (locally)

            [hack hack hack on subsystem 1...]
            git add <file1...>
            git commit
            [hack hack hack on subsystem 2...]
            git add <file2...>
            git commit

* push to personal fork
* pull request on GitHub
* [finish a feature](#finish_feature)


**Alternatively**, you can request write access to the official repository and push your branch directly there:

    git push -u origin <mybranch>

this will be discussed in the use cases now:

Several use cases
-----------------

* single project vs multiple projects
* fix a bug (light, needs to be quickly fixed) vs develop a new feature (heavier change)
* the user may or may not have push permission on the `origin` repository

|                      | fix a bug | develop a feature |
| -------------------- | --------- | ----------------- |
| user has push access | push to master or [feature branch](#feature_branch) | [feature branch](#feature_branch) |
| no push access       | [feature branch](#feature_branch) | [feature branch](#feature_branch) |

Basically we handle multiple projects the same way as working in a single project, we just need to perform commpi / push / pull request operations multiple times. But we just needed tools to help automatize at least update operations in multiple projects.


<a name="bv_maker_sources">

what bv_maker sources does / should do:
---------------------------------------

* fetch all remotes
* use git-lfs if available
* initialize on origin/[branch] (new), formerly: on a detached branch
* **\[TODO]** fast-forward from upstream (personal fork/branch)
* fast-forward from origin/[branch]
* **\[TODO]** fast-forward all local branches

The following operations are **not performed** by ``bv_maker sources``:

* any merge or operation which would insert a new commit in the git history.
    Thus merge operations should be done manually, for instance from a feature branch:

            git merge origin/master

* forward remote branches of the personal fork:
    local branches will be updated, but will not propagate to the personal fork.
    \[**TODO:** should it ?]
    This has to be done manually:

            bv_maker sources
            git checkout master
            git push my_fork

* update in cases when branches have diverged.
    When you have made changes that diverge from the ``master`` branch, ``bv_maker`` will be unable to incorporate upstream changes into your repository. You will have to incorporate them manually into your branch:

            cd <repository>
            git pull origin master


### bv_maker 2 vs 3

With bv_maker v2, working on repositories managed by bv_maker sources used to have one peculiarity: `bv_maker` used to set up the repositories in detached mode, which means that no local branches are created in the repositories. This means that you have to create your own branch as soon as you want to commit changes, using ``git checkout -b <branchname>``.

With bv_make v3, this behavior is no longer used: repositories are completely cloned, then the selected branch (`master` or `integration`) is activated.

For client repositories created using bv_maker 2 and now used with bv_maker 3, the detached branch mode is still maintained, and will be until you switch to (checkout) a "real" branch. The difference is that repositories will be fully fetched (as in bv_maker 3).


Helper tools
------------

**\[TODO]:**

    bv_maker status

will show the status of source branches in multiple projects (current branch, modified code, staged changes, local commits not pushed to a remote, diverging branches and remotes...)

There is also possibility to use [vcstool](https://github.com/dirk-thomas/vcstool)


<a name="feature_branch">

Working with feature branches
-----------------------------

<a name="forking">

### with a fork (recommended)

If not already done:
* Fork on github.
  Follow the steps below if you do not yet have a personal fork of the project:

    1. Create an account on https://github.com/ if you do not already have one.
    2. Fork the repository: go to its GitHub page, then click the Fork button on the top right:

        ![Fork button](images/Bootcamp-Fork.png)

        You can read the [GitHub guide on forking projects](https://guides.github.com/activities/forking/) to learn more.

* Add your personal fork as a _remote_ in your local repository:

        git remote add myfork https://github.com/<username>/<repo>.git

    or:

        git remote add myfork git@github.com:<username>/<repo>.git

* fetch/pull that remote:

        git fetch myfork

* create a feature branch

        git checkout -b new_feature myfork/master

* develop/modify code
* commit locally:

        git add <source_files>
        git commit

* Finally, push the current branch on your personal fork:

        git push
        # or:
        git push myfork new_feature

When the feature is done and OK:

* create a pull request on github
* [finish and cleanup](#finish_feature)

### without a fork (needs permission to create branches on origin)

* checkout / update master

        git checkout master
        git pull

* create a local branch

        git checkout -b new_feature origin/master

* develop/modify code
* commit locally:

        git add <source_files>
        git commit

* push to the remote repository in the feature branch:

        git push
        # or:
        git push origin new_feature

When the feature is done and OK:

* create a pull request from origin/new_feature to origin/master or origin/integration
* [finish and cleanup](#finish_feature_origin)

all users will see the feature branch, please don't make thousands of feature branches, or at least clean them up once they are merged.

<a name="finish_feature">

Finish a feature
----------------

### with a personal fork

* pull request
* switch to master

        git checkout master

* set-upstream-to origin/master
* when the PR is closed:
    * delete the local branch:

            git branch -d new_feature

    * delete the branch on your fork

            git push john :new_feature

    * eventually cleanup branches:

            git remote prune john

<a name="finish_feature_origin">

### with a feature branch on the origin remote

* pull request
* switch to master
    git checkout master

* when the PR is closed:
    * delete the local branch:

            git branch -d new_feature

    * delete the branch on the remote repository

            git push origin :new_feature

    * eventually cleanup branches:

            git remote prune origin

Possibly needs some specific permissions. If you cannot perform some steps, please ask someone who has the permissions (the person who merges the pull request).

Howto / common situations
-------------------------

* I have started to modify source code without checking the state / branch of my repository

        git stash
        git checkout -b new_branch
        git stash pop
        git add <files>
        git commit
        [ git push ]

* same but I have also commited locally, on the wrong branch

    * if only one commit has been done: use cherry-pick

            git status
            # note the commit id if you are in a detached branch, and the previous one
            # (you can use gitg for that)
            git checkout -b new_branch origin/master
            git cherry-pick <commit_id>
            git push
            git checkout <wrong_branch>
            git reset --hard <previous_id>

    * if several commits have been done:
        get the commit ids of the last commit (`<commit_id>`), and of the last one __before__ the first commit you have to move (`<previous_id>`)

            git rebase --onto master <previous_id> <commit_id>
            git checkout <wrong_branch>
            git reset --hard <previous_id>

* https / ssh URL or github projects:
    URLs in github may use the *https* protocol (not needing a ssh key, but git will ask for a password every time you push), or the *ssh* protocol, using a ssh key which will avoid the need for a password everytime. bv_maker uses https by default because it cannot assume you have a github account and have provided a ssh key in github. But it's more convenient to work with ssh. So we have to switch when we have a github account and a ssh key registered in it.
    There is apparently a tool to switch automatically. **\[TODO]** find and describe it.
