---
title: Code Highlighter
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
tags:
- Github blog
- Code highlighter
toc: true
toc_sticky: true
markdown: kramdown
highlighter: rouge
categories:
- Github Blog
---

# Java
```java
public static void main(String args[]) {
  System.out.println("Hello world");
}
```

# Python
```python
print("Hello world")
```

# Dockerfile
```dockerfile
FROM ubuntu:18.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

# Shell
```shell
for i in {1..5}
do
   echo "Hello world $i"
done
```