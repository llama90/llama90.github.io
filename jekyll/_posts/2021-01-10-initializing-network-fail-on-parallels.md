---
title: macOS(Big Sur)에서 패러렐즈 네트워크 초기화 실패
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Parallels
- macOS
- Network
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Trouble Shooting
---

맥OS를 Big Sur로 업데이트하고 패러렐즈를 실행하니 다음과 같은 에러 메시지가 발생하면서 인터넷이 되지 않았다.

```
네트워크 초기화에 실패했습니다.
```

참고한 사이트를 바탕으로 아래와 같은 명령을 수행했고, 정상적으로 실행하는 것을 확인했다.

```shell
sudo -b /Applications/Parallels\ Desktop.app/Contents/MacOS/prl_client_app
```

# Reference
* [맥북 프로 빅서 업데이트 후 패러럴즈 PARALLELS 오류 해결 방법](http://blog.naver.com/PostView.nhn?blogId=uplife1&logNo=222145172013&parentCategoryNo=43&categoryNo=&viewDate=&isShowPopularPosts=true&from=search)
