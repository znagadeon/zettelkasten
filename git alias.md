---
publishName: git-alias
url: https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-Git-Alias
---

# Git Alias

`git config`를 이용하여 명령의 alias를 만들 수 있다.

```sh
git config --global alias.co checkout
git co # git checkout과 같은 동작

# 여러 단어로 된 명령어를 alias하기
git config --global alias.unstage 'reset HEAD --'
git unstage filename # git reset HEAD -- filename과 같은 동작
```
