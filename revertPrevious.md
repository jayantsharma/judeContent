git - Revert to a previous commit

Reverting to a previous commit is an inherently ambiguous task and can mean different things to different people. We consider a git repo with the following history, and the different scenarios therof. Let's say the dev wants to *revert* to the commit `dbc1e1f`.

```sh
$ git log -5
commit 6ba705598ba44a10e9bddbd3ceb5b4a6858ca61d
Author: Me <me@gmail.com>
Date:   Fri Dec 18 20:10:18 2015 +0530

    foo

commit 2c7aa2b3b3fbcde2c2faac19394ea910564f9833
Author: Me <me@gmail.com>
Date:   Fri Dec 18 19:58:49 2015 +0530

    boo

commit 1f59c26024cab0777c4fe1e93c176228105e75ef
Author: Me <me@gmail.com>
Date:   Fri Dec 18 19:10:00 2015 +0530

    zoo

commit 1d99af2b59caa0ddf4ccbedca4acd26796d7e2b9
Author: Me <me@gmail.com>
Date:   Tue Dec 15 20:44:19 2015 +0530

    hoo

commit dbc1e1f757fba07b1e16eeeee1e7e1bf81bc2843
Author: Me <me@gmail.com>
Date:   Tue Dec 15 20:20:34 2015 +0530

    loo
```

- ### Temporary Switch to `<commit>`
If the aim is to go back to the older state and look around, the simply checking out the commit will be sufficient.
```sh
# DETACHED HEAD state,i.e., no branch checked out
git checkout dbc1e1f
```
In case new commits need to be made there, a new branch should be created from that commit : 
```sh
git checkout -b oldState dbc1e1f
```

- ### Getting Rid of commits
How one proceeds with in this scenario largely depends on whether the commits have been published or not ?

1. Unpublished commits
If the commits haven't been published, altering the history isn't a concern. With `git reset`: 
```sh
# will remove all modifications to working directory and index
# use --mixed or --soft if this is not what you want
git reset --hard dbc1e1f

# alternatively, to keep local changes since last commit
git stash
git reset --hard dbc1e1f
git stash pop
# saves changes, then re-applies changes after resetting
# could possibly get merge conflicts

2. Published commits
Instead of re-writing history with `git-reset`, one should use `git-revert` in the case of published commits. `revert` creates new commits with corresponding reverse-patches applied for the stated commits. 
```sh
# create four revert commits
git revert 6ba7055 2c7aa2b 1f59c26 1d99af2

# can specify ranges also; see gitrevisions for detail
# reverts the last four commits
git revert HEAD~4..HEAD

# OR get your index and working dir into the desired state
git checkout dbc1e1f .
# then commit with an informative message
git commit
```

Using the `--no-commit` flag with `git-revert` applies the relevant reverse patches to the working directory and index *without making a new commit*. After one is satisfied with the changes, a *single* new commit can be created which gets the state back to the commit being *reverted* to. Contrast this with having a reverse-patch commit for every commit stated.
```sh
git revert --no-commit HEAD~4..HEAD
# handle conflicts
git revert --continue
```
