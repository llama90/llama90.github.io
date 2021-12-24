---
title: Git Scrapbook
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Git
- Scrapbook
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Scrapbook
---

# [github 잘못 올라간 파일 히스토리까지 삭제하기](https://donologue.tistory.com/373)
```shell
$ git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch [파일 경로와 이름]' --prune-empty -- --all
$ git push origin [브랜치 명] --fore
```

# [github 하나로 1인 개발 워크플로우 완성하기](https://www.huskyhoochu.com/issue-based-version-control-201/)

## 이슈 만들기

## 로컬 저장소에 브랜치 생성
```shell
$ git checkout -b [issue number]-[issue summary]
$ git push --set-upstream origin [issue number]-[issue summary]
```

## 목표 해결 && 테스트 

## 커밋과 푸시
```shell
$ git commit -m "#[issue number] commit contents" 
$ git commit --amend
...
Resolves #[issue number]
$ git push
```

## 메인 브랜치에 병합
```shell
$ git rebase dev
$ git checkout dev
$ git pull --rebase=preserve
$ git merge [issue number]-[issue summary]
```
* rebase: 현재 브랜치의 커밋 히스토리에 병합할 브랜치의 히스토리를 합쳐서 두 브랜치의 루트를 하나로 재정립
* `git pull --rebase=preserve`: 원격 브랜치의 최신 사본을 업데이트 받는 명령어

## 이슈 닫기
```shell
$ git push origin -d [issue number]-[issue summary] && git branch -d [issue number]-[issue summary]
```

# [포크한 깃허브 저장소를 원본 저장소와 동기화 하기](https://hyunjun19.github.io/2018/03/09/github-fork-syncing/)
```shell
$ git remote -v
$ git remote add upstream [remote repository address]
$ git remote -v
$ git fetch upstream
$ git merge upstream/master
```
* git remote -v : 설정된 리모트 저장소 확인
* git remote add upstream [remote repository address] : 리모트 저장소 추가, upstream은 추가되는 저장소의 이름으로 변경 가능
* git remote -v : 설정된 리모트 저장소 확인
* git fetch upstream : 이름으로 지정한 저장소의 코드를 fetch
* git merge upstream/master : 현재 레포지토리에 리모트 저장소의 코드를 merge
* git push : fork된 저장소로 push

# [Git add, commit, push 취소하기](https://velog.io/@hidaehyunlee/Git-add-commit-push-%EC%B7%A8%EC%86%8C%ED%95%98%EA%B8%B0)
```shell
$ git reset --soft HEAD^
$ git reset --mixed HEAD^
$ git reset HEAD^
$ git reset HEAD~2
$ git reset --hard HEAD^

$ git reflog 
$ git reset HEAD@[number] or $ git reset [commit id]
```

# [Git remote branch 가져오기](https://cjh5414.github.io/get-git-remote-branch/)
```shell
$ git remote update
$ git branch -r
$ git checkout -b [branch name to create] [remote branch]
```

# [Git 커밋 메시지 수정하기](https://velog.io/@mayinjanuary/git-%EC%BB%A4%EB%B0%8B-%EB%A9%94%EC%84%B8%EC%A7%80-%EC%88%98%EC%A0%95%ED%95%98%EA%B8%B0-changing-commit-message)
```shell
$ git rebase -i HEAD~{n}
수정하고자 하는 커밋에 대해 pick -> edit로 수정
$ git commit --amend
$ git rebase --continue
$ git push -f
```
