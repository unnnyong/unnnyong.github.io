---
layout: post
title:  "iOS | 🇰🇷  drawRect 와 setNeedsDisplay"
date:   2019-05-29 00:28:55 +0900
categories: iOS
---

# iOS | 🇰🇷  drawRect 와 setNeedsDisplay

이 두 가지는 어느 타이밍에 실행되는지가 가장 큰 차이점이 있습니다.

### `func draw(_ rect: CGRect)`

[🔗 공식문서](https://developer.apple.com/documentation/uikit/uiview/1622529-draw)

#### 어느 때 불려지는 메소드인가

뷰는 `func draw(_ rect: CGRect)`로 그려집니다.
뷰가 처음 생성될 때에는 `func draw(_ rect: CGRect)`로 뷰가 그려지게 됩니다.

#### 💡상속받은 custom class에서 사용될 때에는 super를 늘 불러야하나?

`UIView`의  `func draw(_ rect: CGRect)`는 **기본적으로 아무 처리도 하지 않기 때문**에,  `UIView`를 상속받은 custom view의 경우에는 `super.rect(rect)` 를 **부르지 않아도 됩니다.**

하지만 `UILabel`, `UITextView` 등 `UIView`를 상속받은 view를 상속받은 custom view의 `func draw(_ rect: CGRect)`에서는 super를 불러주어야 합니다.

### `func setNeedsDisplay()`

[🔗 공식문서](https://developer.apple.com/documentation/uikit/uiview/1622437-setneedsdisplay)

#### 어느 때 불려지는 메소드인가

뷰가 새로 그려져야할 때, reload가 필요할 때, 개발자가 실행시키는 메소드입니다.

#### 혼동해서 사용되는 메소드들과는 무엇이 다른가

1. `setNeedsDisplay`

- 뷰가 처음 생성될 때 레이아웃이 구성되는 사이클을 처음부터 실행시키는 메소드입니다.
- 뷰에 추가된 모든 subView를 전부 새로 그릴 필요가 있을 때 사용되는 메소드입니다.

2. `layoutIfNeeded`

- 화면 재구성이 필요해지면 view와 그 뷰의 subView가 전부 재배치됩니다.
- 예를들어 `hogeView.layoutIfNeeded()`를 실행한 경우
- hogeView와 hogeView에 추가된 subView들이 super view의 레이아웃이 재설정되면 바로 새로 재배치됩니다.
- trigger를 설정하는 느낌의 메소드이기에 `setNeedsDisplay`, `setNeedsLayout` 보다 무겁습니다.
- 이 메소드는 Main thread에서 볼려져야합니다 !!!! (당연한 얘기)

3. `layoutSubviews`
- iOS가 화면을 새로 그릴 때 부르는 메소드로 직접 부르는건 안됩니다 !!!!!!!! ⚠️


### 정리

- view가 처음 생성될 때 레이아웃 설정을 추가하고 싶다 👉 `func draw(_ rect: CGRect)`
- 모든 superView의 모든 subView를 바로 새로 그리고 싶다  👉 `func setNeedsDisplay()`
- 특정 view와 그 view에 추가된 subView를 새로 그리고싶다 👉 `layoutSubviews`


#### 참고
- [UIKit徹底解説 iOSユーザーインターフェイスの開発](https://www.amazon.co.jp/dp/B00L318P4I/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)
- [「setNeedsDisplay」、「setNeedsLayout」、「layoutIfNeeded」、「layoutSubviews」の違い](https://qiita.com/h1d3mun3/items/467c9a16d30b5de73969)
