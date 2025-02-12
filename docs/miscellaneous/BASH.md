# Useful Commands
Remove local branches that do not exist anymore in git remote. You can give an alias to this command in the `zshrc` file.
```bash
git fetch -p && for branch in $(git branch -vv | grep ': gone]' | awk '{print $1}'); do git branch -D $branch; done
```