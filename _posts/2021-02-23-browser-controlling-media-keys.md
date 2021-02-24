---
layout: post
date: 2021-02-23
title: 브라우저가 키보드의 멀티미디어
subtitle: 음악 재생, 이전/다음 곡 등 멀티미디어 특수 키 기능이 있는 키보드가 브라우저에서 재생되고 있는 동영상을 조작하지 않게 하자.
tags: chrome firefox tech-tips
---

# 소개

iTunes, Spotify 등 음악 재생 프로그램을 사용하면 키보드의 멀티미디어 키로 재생/정지, 이전/다음 곡으로 넘어가기 등 단축키로 음악을 조작할 수 있다. 하지만 최신 브라우저 패치들로 인해 동시에 유튜브, 트위치 등 동영상이 재생중인 영상이 키보드의 멀티미디어 키를 이용해 조작되는 경우가 있다.

다음과 같이 설정을 바꾸면 키보드의 음악 재생/정지, 이전/다음 곡 키가 브라우저를 조작하지 않게 된다. 또한 윈도우즈 10의 경우 "현재 재생 중"으로 유튜브 영상의 제목이 뜨지 않게 할 수 있다.

# 해결 방안

## 파이어폭스

[about:config](about:config) 에 들어가서 `media.hardwaremediakeys.enabled` 을 검색해서 설정을 해제한다.

## 구글 크롬

[chrome://flags](chrome://flags) 에 들어가서 `hardware-media-key-handling` 으로 검색해서 설정을 끈다.

(빠른 링크: [chrome://flags/#hardware-media-key-handling](chrome://flags/#hardware-media-key-handling))

---

작업을 하다가 음악 프로그램을 잠시 멈추려고 누른 "일시 정지" 키가 시청중이던 트위치 방송 영상을 멈춰버리거나, 반대로 음악 재생을 하려고 했는데 브라우저 탭 중 하나에 열려 있던 유튜브 영상이 재생되어버리거나 하면 가끔 너무 짜증이 났다.
