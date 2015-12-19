# Git - Checkout remote branch

The first step in working on a remote branch would be to fetch the contents of the remote repository : 
```sh
git fetch origin
```

After the above step, a *local reference* is set up to track the remote branch, if it didn't exist before. The branches available for checkout can be seen with : 
```sh
# use -r instead of -a to view only remote branches
git branch -v -a
```
Let's assume the remote branch name is *foo*. The corresponding remote tracking branch would be named *origin/foo*. To checkout the remote branch, a local copy needs to be created on which the dev can work. The logic behind this can be understood as that, Git does not allow one to work on *someone else's branch*. Every contributor needs their own copy of the branch in order to work.
```
git checkout -b localFoo --track origin/foo

# short for
git branch localFoo --track origin/Foo
git checkout localFoo
```
The `--track` option above sets up the upstream config for the local branch *localFoo*. It may be simply omitted, in which case no upstream config will be set or replaced with `--no-track`. See `man git-branch` for details. 
With **Git versions >= 1.6.6**, one can simply do : 
```sh
# DWIM
git checkout foo
```
This effectively creates a *__local__ branch foo* tracking the remote branch *foo*. Note that the method only works if there's only one <remote> found with the tracking branch *foo*. 

# References
1. [Git Book: Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)
3. [GitGuys Tut on remotes](http://www.gitguys.com/topics/adding-and-removing-remote-branches/)
