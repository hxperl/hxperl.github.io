---
layout: post
title:  "error: Multiple commands produce"
date:   2018-11-27 17:27:09
author: hxperl
---



# Xcode build error

Xcode 에서 빌드할 때 Multiple commands produce 에러가 나는 경우

하나의 앱에 여러개의 plist가 존재 하는 이유일 가능 성이 크다.

*Solution -> Open target -> Build phases > Copy Bundle Resources* and remove `info.plist` from there.