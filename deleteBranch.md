# git - Deleting Branches

Consider a local branch *foo*. The corresponding remote-tracking branch *origin/foo* (origin is the remote repo) tracks the *remote branch Foo*. The task of deleting branch foo can be interpreted as the task of deleting all the above three branches. To enumerate : 
1. *local* branch foo
2. *remote* branch Foo
3. local *remote-tracking* branch origin/foo

- ### Deleting *local* branch
The local branch foo can *only* be deleted if it is fully merged in its upstream branch, or in *HEAD* if no upstream was set with `--track` or `--set-upstream`. The following command accomplishes this : 
```sh
git branch -d foo
```
To force deletion if the above requirements are not met : 
```sh
git branch -D foo
```

- ### Deleting *remote* branch
The old, rather obscure syntax for deleting a remote branch is this : 
```sh
git push origin :Foo
```
interpreted as deleting the remote branch Foo, because nothing is being pushed to it (`git push origin local:remote`).   
As of **Git v1.7.0**, remote branches can be deleted using : 
```sh
git push origin --delete Foo
```
There's another way to delete the remote branch Foo,
```sh
git push --all --prune origin
```
This deletes **all** remote branches that do not exist locally. Since the command is potentially harmful, the `--dry-run, -n` option can be used initially. Yet more powerful is 
```sh
git push --mirror origin 
```
which specifies that all refs under _refs/_ be mirrored to the remote repo origin.

- ### Deleting tracking branch
After deleting the remote branch Foo, all contributors to the repository can run 
```sh
git remote prune origin 	# obsolete
```
OR
```sh
git fetch --prune origin
```
to remove remote-tracking references that do not exist on the remote.  

Alternatively, the tracking branch can be explicitly deleted using 
```sh
git branch -d -r origin/foo
```

### References
1. [Git Book: Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)
2. [Git Book: Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
3. [GitGuys Tut on remotes](http://www.gitguys.com/topics/adding-and-removing-remote-branches/)