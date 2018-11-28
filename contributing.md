---
title: BrainVISA contributor's guide
---

General workflow
----------------

TODO describe the role of bug_fix, trunk, master, integration


Contributing to projects hosted on Github
-----------------------------------------

Many components of BrainVISA now have their source managed with Git, and are hosted on Github. If you are new to Git, you should read the short [GitHub Git handbook](https://guides.github.com/introduction/git-handbook/). You can find more information in the [Git and GitHub learning resources](https://help.github.com/articles/git-and-github-learning-resources/).

Working on repositories managed by ``bv_maker sources`` has one peculiarity: ``bv_maker`` will set up the repositories in _detached_ mode, which means that no local branches are created in the repositories. This means that you should create your own branch as soon as you want to commit changes, using ``git checkout -b <branchname>``.

Here is a typical workflow for making a contribution:

1. Get the sources and compile with ``bv_maker``.

2. Create a local branch in the repository that you want to contribute to (for example _soma-io_):

       cd <sources>/soma/soma-io/master
       git checkout -b mybranch

3. Modify the code, test, commit your changes. If you make substantial changes, you are encouraged to make intermediate commits that separate your changes into logical units.

       [hack hack hack on subsystem 1...]
       git add <file1...>
       git commit
       [hack hack hack on subsystem 2...]
       git add <file2...>
       git commit

4. Now your work is saved in your local repository. If you want to share it with others, you can _push_ your branch to GitHub to make it accessible.

   Follow the steps below if you do not yet have a personal fork of the project:

   1. Create an account on https://github.com/ if you do not already have one.

   2. Fork the repository: go to its GitHub page, then click the Fork button on the top right:

      ![Fork button](images/Bootcamp-Fork.png)

      You can read the [GitHub guide on forking projects](https://guides.github.com/activities/forking/) to learn more.

   3. Add your personal fork as a _remote_ in your local repository:

          git remote add -f myfork https://github.com/<username>/<repo>

   Finally, push the current branch on your personal fork:

       git push -u myfork <mybranch>

   **Alternatively**, you can request write access to the official repository and push your branch directly there:

       git push -u origin <mybranch>

5. When you have made changes that diverge from the ``master`` branch, ``bv_maker`` will be unable to incorporate upstream changes into your repository. You will have to incorporate them manually into your branch:

       cd <repository>
       git pull origin master

6. When you consider that your branch is ready to be released, [create a Pull Request](https://guides.github.com/activities/forking/#making-a-pull-request) so that the maintainers of the project can review it and merge it into the ``master`` branch.
