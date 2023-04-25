# Git에서 특정 브랜치의 마지막 커밋 해시 가져오기

```sh
git rev-parse {branch name}

# 응용: 특정 브랜치의 마지막 커밋을 체리픽
git cherry-pick $(git rev-parse another-branch)
```
