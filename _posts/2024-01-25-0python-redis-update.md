---
title: redis update 시 한글 깨짐 현상
author: yjh
categories: [Trouble Shooting]
tags: [python, trouble shooting]
---

## 문제

Redis 에 `json`으로 update 시 한글이 깨지는 문제가 발생했다.

## 해결

`json.dumps()` 시에 `ensure_ascii = False` 로 지정해준 뒤 `.encode("utf-8")`을 해주면 된다.
