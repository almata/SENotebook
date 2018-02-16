# Add and remove remotes (like GitHub, Bitbucket and so on)

Check current remotes:

```bash
$ git remote -v
```

Remove one remote (for instance the one called `origin`):

```bash
$ git remote rm origin
```

Add a new remote (for instance a new one called, again, `origin`):

```bash
$ git remote add origin https://github.com/almata/myrepo.git 
```

Finally, to set the remote as upstream and push the current branch:

```bash
$ git push --set-upstream origin master
```

From here on, usual `git push` is perfectly fine.
