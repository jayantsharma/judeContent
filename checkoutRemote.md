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
Let's assume the remote branch name is *foo*. The corresponding remote tracking branch would be named *origin/foo*. To checkout the remote branch, a local copy needs to be created on which the dev can work. 
```sh
git checkout -b localFoo --track origin/foo

# short for
git branch localFoo --track origin/Foo
git checkout localFoo
```
The `--track` option above sets up the upstream config for the local branch *localFoo*. It may be simply ommitted or replaced with `--no-track`.  
With Git versions >= 1.6.6, one can simply do : 
```sh
# DWIM
git checkout foo
```
The method only works if there's only one <remote> found with the tracking branch *foo*. 

# References
1. [Git Book: Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)
2. [Git Book: Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
3. [GitGuys Tut on remotes](http://www.gitguys.com/topics/adding-and-removing-remote-branches/)
