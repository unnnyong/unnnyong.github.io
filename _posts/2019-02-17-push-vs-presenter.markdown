---
layout: post
title:  "iOS | 🇰🇷 present vs push"
date:   2018-11-28 00:10:54 +0900
categories: iOS
---
# iOS | 🇰🇷 present vs push

iOS에서 화면이 이동될 때 아래 방향에서 새로운 화면이 나오거나, 오른쪽에서 새로운 화면이 나오는 두 가지 뷰 전환 방법이 있다.

modal로 세로로의 뷰 전개가 이루어지는 것이 `present`, 가로로 뷰 전개가 일어나는 것이 `push`이다.

코드를 작성하면서 위와 같은 방향성에 대한 이해만 있었기에 자세히 살펴보고 기록하기 위해 글을 작성해보려 한다.

이 글에서 살펴볼 것은 세 가지이다.

1. 각각 어느 class로부터의 메소드인가
2. 적절한 사용은?
3. 이전 뷰로 돌아오는 메소드는 무엇인가

자, 고고싱 ~ ! 🚁🚁

---

### present

[🔗 present 공식문서링크](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621380-present)

```swift
func present(_ viewControllerToPresent: UIViewController,
            animated flag: Bool,
            completion: (() -> Void)? = nil)
```

![presen_vc](https://user-images.githubusercontent.com/28520053/52913740-6c37e900-3304-11e9-965e-9a890832fa01.gif)


1. UIViewController의 method이다.
2. 뷰 전개 방향이 세로 방향이다.

위 두 가지가 **present** 에 대한 중요한 포인트이다.

공식 문서에 나와있는 한 줄 설명은 다음과 같다.

> Presents a view controller modally.

> View Controller를 modally 보여준다(나타내어준다).

**modally** 는 네이버 영어사전에서는 `양식(樣式)으로, 형식으로, 형태상으로.`라는데 사전적 정의를 알고 나니 괜히 더 복잡해진 것 같다.

iOS에서 사용되는 **modally** 를 이해하기 위해서는 HIG(Human Interface Guidelines)의 Modality에 대한 정의를 살펴보는게 이해하기 쉽다. [🔗](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/modality/)

![2019-02-17 10 23 44](https://user-images.githubusercontent.com/28520053/52913734-6b06bc00-3304-11e9-8f46-8a8cf7bb32a1.png)


**modally** 는 A라는 뷰 위에 B라는 뷰를 정의함으로써 샌드위치처럼 한 겹씩 올라간 구조라고 이해하면 된다. `present` 는 뷰의 이동이라기 보다는 `UIViewController` 위에 뷰를 켜켜이 쌓는 동작이라고 생각하는게 옳은 것 같다. 그리고 전의 뷰로 돌아가기 위해서는 한 겹씩 없애는 동작이 이루어진다.

![present_alert](https://user-images.githubusercontent.com/28520053/52913739-6b9f5280-3304-11e9-89ae-1a731adb6aaa.gif)


애플은 화면 전체를 가리는 view 뿐만 아니라 alert, mail app의 새로운 mail 작성과 같은 존재하는 view 위에 무언가를 띄우는 뷰도 **present** 로 생성하고 있다. 이것은 push와 가장 다른 차이점이기도 하다.

UINavigationController를 사용한다는 전제하에, present를 사용하게 되면

**`UIViewController` 위에서 연속적인 흐름과는 맞지않게, 갑자기 아래에서 나타난 view를 보여주는 메소드가 `present` 이다.**

`present`는 다른 화면으로의 뷰의 이동은 맞지만, 나타난 `UIViewController`는 연속적인 화면의 뷰보다는 Alert와 같은 유저 흐름을 깨는 갑작스러운 뷰를 띄우기에 더 적절하다.

#### completion이 실행되는 타이밍

```swift
func present(_ viewControllerToPresent: UIViewController,
            animated flag: Bool,
            completion: (() -> Void)? = nil)
```

parameter 중 completion handler는 새로운 뷰의 viewDidAppear 후에 실행된다.

#### dismiss

```swift
func dismiss(animated: Bool, completion: (() -> Void)? = nil)
```

![present_vc_dismiss](https://user-images.githubusercontent.com/28520053/52913737-6b9f5280-3304-11e9-9d0c-8377323e3259.gif)

**현재의 presenter 로 열려진 가장 최상단의 view를 거둬내는 메소드.**

`present`로 view를 새로 띄운 후, 전 화면으로 돌아가기 위해서는 `dismiss`를 사용한다.

영어사전에서 찾아낸 `dismiss`의 의미가 꽤 여러개 있었는데, iOS 내 메소드 의미와 딱 맞는 의미는 **퇴거시키다** 이다.

completion handler는 View Controller가 사라진 후 실행된다.

### push

![push](https://user-images.githubusercontent.com/28520053/52913741-6c37e900-3304-11e9-9130-82695e2fb030.gif)


[🔗 push 공식문서링크](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621887-pushviewcontroller)

push는 [`UINavigationController`](https://developer.apple.com/documentation/uikit/uinavigationcontroller)의 메소드이다.

navigation이 이동하는 위치대로 **가로** 로의 view의 이동이다.

![2019-02-17 10 24 55](https://user-images.githubusercontent.com/28520053/52913733-6b06bc00-3304-11e9-9162-41203830b5ec.png)

`UINavigationController`의 navigation stack인 [`viewControllers`](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621873-viewcontrollers) 배열에 새로운 View Controller를 추가시키고, 새로운 View Controller를 [`topViewController`](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621849-topviewcontroller)으로 설정해 user의 view에 보여준다.

#### pop

[🔗 pop 공식문서링크](https://developer.apple.com/documentation/uikit/uinavigationcontroller/1621886-popviewcontroller)

pop은 UINavigationController에서 가장 상단에 위치되어 user에게 보여지고 있는 View Controller를 하나하나 거둬내고 아래에 위치한 View Controller를 보여주기 위한 용도로 사용된다.

화면만 살펴본다면 present의 dismiss와 하는 일은 같다.

---

## 🦁 정리

#### present

- 세로 방향으로 전개되는 View
- `UIViewController`의 메소드
- 실행시키면 기존의 `UIViewController` 위에 새로운 뷰를 띄운다.
- 이전 뷰로 돌아가기 위해서는 `dismiss`

#### push

- 가로 방향으로 전개되는 View
- `UINavigationController`의 메소드.
- 실행시키면 기존의 `UIViewController` 위에 새로운 뷰를 띄운다. navigation에 새로운 VC를 쌓는 개념.
- 이전 뷰로 돌아가기 위해서는 `pop`