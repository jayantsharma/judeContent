# git - How to undo the last commit ?

Consider a java-based project, where the developer mistakenly commits `.class` files instead of the relevant `.java` files to git. How can this mistake be rectified ? 

1. ### Undo a commit and redo, using `git-reset`
```
$ git commit -m 'bad commit'
$ git reset --soft HEAD^	(1)
$ edit				(2)
$ git commit -a -c ORIG_HEAD	(3)
```

1. This is most often done when the commit is incomplete, or the commit message has been mis-spelt, or both. Leaves working tree *and* index as it was before "reset".
2. Make corrections to working tree files.
3. "reset" copies the old head to .git/ORIG\_HEAD; re-does the commit by starting with its log message. If the message needs no further editing, the -C option can be given instead.

It would be a good idea after the undo process to add the `.class` files to `.gitignore`.

In the above example, `git-reset` is used with the option `--soft`. Two other options are frequently used with `git-reset` : 
1. `git reset --mixed HEAD^`
This is the same as `git reset HEAD^`. With this option, the working tree remains intact but the index is reset (i.e., the changed files are preserved but not marked for commit). 
2. `git reset --hard HEAD^`
The `--hard` option resets the index and working tree. Any changes since <commit> (here, HEAD^) are discarded.

Note : ^ is a continuation character in DOS. Instead, `HEAD~1` should be used.

2. ### Undo commit to wrong branch
In the event that a commit has been made to the wrong branch, the following can be done : 
```
$ git commit -m 'commit to wrong branch'
$ git reset --soft HEAD~1
$ git checkout rightBranch
```
The developer has switched to rightBranch with all the changes preserved in the index. 

Alternatively,
```
$ git commit -m 'commit to wrong branch'
$ git reset --soft HEAD~1
$ git checkout rightBranch
```

In a slightly different case where the changes have been prematurely committed to a branch (say `master`), a hard reset may be used.
```
$ git branch topic/foo		(1)
$ git reset --hard HEAD~2	(2)
$ git checkout topic/foo	(3)
```
1. "topic/foo" is created, branching off from the current HEAD.
2. Unwanted commits discarded from "master".
3. Switched to "topic/foo" to continue working.

3. ### Amending the latest commit
```
$ git rm --cached classFiles	(1)
$ git add javaFiles		(2)
$ git commit --amend		(3)
```
1. Removes the `.class` files from the index, but *not* from the working directory. Without the `--cached` option, files would be removed from the working directory as well.
2. Add the `.java` files to the index.
3. The tip of the current branch is replaced by creating a new commit, with the message from the original commit used as the starting point.

4. ### Using `git-revert`
To undo a commit that has been made public, `git-revert` can be used. It creates a new commit that *reverts* the changes made in <commit> (`git revert <commit>`). For the above case, the dev may use: 
```
$ git revert HEAD			
$ git add javaFiles			
$ git commit -m 'added java file'	
```

In the event that a commit has been made to the wrong branch, `revert` can be used in combination with `cherry-pick` : 
```
$ git revert HEAD			(1)
$ git checkout rightBranch		(2)
$ git reflog wrongBranch		(3)
$ git cherry-pick revertedCommit	(4)
```
1. `revert` the latest commit on wrongBranch; could also do `git reset --hard HEAD~1.
2. switch to rightBranch
3. look at the logs of wrongBranch, particularly noting the SHA for the *reverted commit*
4. apply the above commit on rightBranch

### References
1. []()
