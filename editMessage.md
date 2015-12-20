# Git - Edit Commit Message

It's not uncommon for a developer to discover that a certain commit message is off the mark and needs revision. Like with most things in Git, this can be achieved in more than one way.

1. ### Using `git commit --amend`
The command fires up a message editor with the previous commit's message as the starting point, instead of an empty one. With the `--amend` option, the new commit *replaces* the tip of the current branch. Care should be taken, since all *staged contents will be committed* after running this command. If a simple message edit is all one has in mind, the index should be empty. However, the working directory might have changes and they will not be affected. 
```sh
# fires up message editor
git commit --amend
# pass message on command line
git commit --amend -m "New Message"
```

2. ### Using `git rebase`
If the commit to be fixed is not the most recent one, then one can look to rebase. With rebase : 
```sh
# get commitId with git reflog
git rebase --interactive <commitId_of_parent_flawed_commit>

# alternatively, to edit previous n commits
git rebase -i HEAD~n
```
This brings up an editor listing all the commits since the mentioned one. Change `pick` to `reword` for any of the commits listed. For editing commits beyond the commit message, change to `edit`.  
For each commit that one wants to *reword*, Git drops into the editor; for every *edit*, into the shell. After the required changes have been made *to a commit* : 
```sh
# to continue editing commits with rebase
git rebase --continue
# OR
git commit --amend

# to abort rebase
git rebase --abort
```

## Pushing changes upstream post edit
Dealing with unpublished changes is easy. A simple `git push` will be sufficient.
```sh
git push <remote> <branch>
``` 
A *force push* publishes an amended commit that has already been pushed to the remote repo. 
```sh
git push --force <remote> <branch>
```
Note the two major side-effects of a forced-push : 
1. **The remote branch in over-written**. All commits to the remote that have not been merged with the local branch will be lost.
2. **The history of the publicly-shared branch will be re-written**. This might imply problems for other team-members who have copies of the old commits that have been deleted upstream. Hence, team coordination is important when going through with such a change. See the __Recovering from Upstream Rebase__ section of the `manpage` on `git rebase`.

### References
1. [Git Docs: Rebase](https://git-scm.com/docs/git-rebase)
2. [Git Book: Rewriting History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)
3. [Rewriting History with Git Rebase](https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history)