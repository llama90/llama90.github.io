---
title: macOS에서 node 버전 변경
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Node
- DBeaver
- CloudBeaver
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Trouble Shooting
---

[CloudBeaver](https://github.com/dbeaver/cloudbeaver)를 빌드하고 실행하는데 있어, `CloudBeaver`에서 사용하는 `graphql` 라이브러리 버전과 설치되어 있던 `node` 버전(15.5) 사이에 호환성이 맞지 않다는 메시지를 확인했다. 실행을 위해서 최대 `node` 버전이 14.x인 것을 확인했고, 이와 관련해서 버전 변경을 위한 방법을 찾아봤다.

참고한 사이트를 바탕으로 아래와 같은 명령을 수행했고, 정상적으로 실행하는 것을 확인했다.

```shell
brew search node
brew install node@14
brew unlink node
brew link node@14 --force --overwrite
echo 'export PATH="/usr/local/opt/node@14/bin:$PATH"' >> /Users/abc/.bash_profile
```

# Reference
* [How to switch Node version on macOS](https://www.hurtak.cc/switch-node-version/)
