---
layout: post
title:  "iOS | 🇰🇷 frame 과 bounds"
date:   2019-06-04 00:40:12 +0900
categories: iOS
---

# iOS | 🇰🇷 frame 과 bounds

## 레이아웃

1pt = 1px

## 좌표 기준

- UIKit : 왼쪽 위가 (0, 0)
- Core Graphics : 왼쪽 아래가 (0, 0)

## frame 과 bounds

#### `frame` (CGRect)

- superView의 좌표계에 대한 뷰의 원점과 사이즈를 표시한 값이다.
- 프레임을 기준.

#### `bounds` (CGRect)

- 뷰의 로컬 좌표계를 기준으로 콘텐츠르 원정으로 뷰의 사이즈를 표시한 값이다.
- 경계를 기준.
- ✨ **bounds 의 원점을 변경하면 갖고 있는 subView 들도 위치가 변한다.**

#### 예제

![image](https://user-images.githubusercontent.com/28520053/58814537-dfb9f800-8660-11e9-8cd3-70cf4e1fb023.png)

- `frame`
    - `view.frame.origin = (20, 10)`
    - `view.frame.size = (50, 70)`

- `bounds`
    - `view.bounds.origin = (0, 0)`
    - `view.bounds.size = (50, 70)`

## transform (CGAffinTransform)

- 뷰의 Core Graphics에 대한 2차원 Affine 변환을 적용한다.
    - Affine 변환의 원점은 뷰의 center 이다.
    - 원점을 다른 곳으로 설정하면, view의 레이어가 갖는 `anchorPoint` 프로퍼티의 값도 변하게된다.
