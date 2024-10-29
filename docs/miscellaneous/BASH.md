# Useful Commands
Remove local branches that do not exist anymore in git remote
```bash
git fetch -p && for branch in $(git branch -vv | grep ': gone]' | awk '{print $1}'); do git branch -D $branch; done
```