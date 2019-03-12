---
layout: post
title:  "iOS | 🇯🇵 韓国人iOSエンジニアが見たiOSの日本語キーボードと確定ボタン"
date:   2019-03-13 03:41:11 +0900
categories: iOS
---

# iOS | 🇯🇵 韓国人iOSエンジニアが見たiOSの日本語キーボードと確定ボタン

世界にはいろんな言語があります。

どんな国でも通じる英語、今この記事を書いている日本語。そして私の母国語の韓国語もあります。

今話した３の言語はそれぞれ違うキーボードが使われてます。
iOSのソフトウェアキーボードで英語はローマ字キーボード、韓国語は２ボル式（ハングル専用キーボード）があります。その中でも日本語のiOSのソフトウェアキーボードはキーボードは２つのキーボードが存在します。

２つもある日本語のiOSのソフトウェアキーボードについて話したいです。

## iOSで日本語キーボード
### ２種類のキーボード

日本語のキーボードにはテンキーとローマ字、２つのキーボードがあります。
両手じゃないとテンキーが持ちらん楽ですが私はまだテンキーが難しいです。😔

`テンキー`はガラケーの時から存在した日本語特有のキーボードでテンキーは 10 keys の名前通り１０個のキーで文字を入力する方法です。（外人の自分が説明を書くのはちょっと面白くなる気がします

`ローマ字`は発音通りローマ字を入力して自動変換で文字を入力する方法です。

### 確定ボタン

日本語のキーボードだけにある上の２つのキーボードで共通に存在することは*確定ボタン*です。

日本語を入力する時には発音通りキーボードを押したらひらがな、カタカナ、漢字のサジェストが出ます。

（このサジェストが何を基準に出るのか。誰が作ったのか。会社ごとに開発しているのか。誰かが作って無料に公開したオープンソースなのか気になります。）

## ２つのキーボードと確定ボタンから起きたこと

これについて話したくて今回この記事を書きはじめました。

### 検索の時のサジェスト

私たちは1日何回も検索窓から何かを検索します。

何かを検索しようと思ってキーボードを押すと自動的にサジェストが出ます。

普通の英語キーボード|日本語キーボードの確定対応できてない検索窓|日本語キーボードの確定対応できている検索窓
---|---|---  
![english](https://user-images.githubusercontent.com/28520053/54086975-ea326180-4391-11e9-84a4-5e6ca26f69e1.gif)|![japanese](https://user-images.githubusercontent.com/28520053/54086973-e999cb00-4391-11e9-96b2-40214a421c2d.gif)|![minne](https://user-images.githubusercontent.com/28520053/54086974-ea326180-4391-11e9-904a-92ea6c689d5a.gif)

一番左の英語入力の場合は確定が必要がありません。

その右の２つのキーボードは確定を押す必要があるローマ字キーボードです。

真ん中と一番右の日本語入力検索窓の違いは**確定ボタンを押さなくても表示しているひらがなからサジェストが出るのか**です。

違う理由は違うdelegateメッソドはを使うからです。これついて今回詳しく書こうと思います。🧙🏻‍

#### 解決方法

1. 確定がないキーボードの場合

ローマ字やハングルみたいに確定ボタンがないキーボードの入力からのサジェストを表示する時には[`UISearchResultsUpdating`](https://developer.apple.com/documentation/uikit/uisearchresultsupdating)の[`updateSearchResults(for:)`](https://developer.apple.com/documentation/uikit/uisearchresultsupdating/1618658-updatesearchresults)を活用します。

```swift
extension SearchViewController: UISearchResultsUpdating {
   func updateSearchResults(for searchController: UISearchController) {
       let 入力されたテキスト = searchController.searchBar.text
   }
}
```

上のコードにある通りsearchBarが文字が追加や削除みたいにアップデートをされたらこちらのメッソドが実行されます。上の例のコードでは定数しかないですが、あの中でサジェストをrequestします。

2. 確定ボタンがあるキーボードの場合

正解から書きます。

[`UISearchBarDelegate`](https://developer.apple.com/documentation/uikit/uisearchbardelegate)の[`func searchBar(_ searchBar: UISearchBar, shouldChangeTextIn range: NSRange, replacementText text: String) -> Bool`](https://developer.apple.com/documentation/uikit/uisearchbardelegate/1624328-searchbar)を使うとできます。

```swift
extension SearchViewController: UISearchControllerDelegate {
 func searchBar(_ searchBar: UISearchBar, shouldChangeTextIn range: NSRange, replacementText text: String) -> Bool {
       DispatchQueue.main.asyncAfter(deadline: .now() + 0.1) { [weak self] in
           guard let strongSelf = self else {
               return
           }

           strongSelf.reloadTableView(with: searchBar.text ?? "")
       }

       return true
   }
}
```

上の例文を見て引数の`text`を使うとdelayする必要もないもっと簡単に解決できない？と疑問がある方もあると思います。

```swift
extension SearchViewController: UISearchControllerDelegate {
 func searchBar(_ searchBar: UISearchBar, shouldChangeTextIn range: NSRange, replacementText text: String) -> Bool {
       let pastSearchKeyword = searchBar.text ?? ""

       guard let range = Range(range, in: pastSearchKeyword) else {
           return true
       }

       let currentSearchKeyword = pastSearchKeyword.replacingCharacters(in: range, with: text)
       reloadTableView(with: currentSearchKeyword)

       return true
   }
}
```

上の感じでrangeとtextを活用してももちろん確定ボタンを押さなくてもキーボードをタップして検索窓のテキストに変化があった時に検知ができます。

⚠️ しかし、この時には**注意点が１つあります。**

日本語のローマ字キーボードの場合が問題になります。ローマ字はユーザーがタップした文字は`a`ですが実際検索窓に表示される文字は`あ`です。

**引数の`text`を使うと`a`が認識されてしまします。`searchBar.text`は実際ユーザーが入力したかった`あ`が認識できます。**

もちろんテンキーキーボードの場合にはこれを考えてなくても大丈夫です。

しかし、ユーザーをテンキーに特定するのはできませんでしたので自分の場合は`searchBar.text`を使うコードに実装しました。

3. 2以外に確定を押さなくても処理する方法はないか？

`searchBar.text`を使うと解決はできますがdelayで大丈夫なのか気になりました。
上のUISearchBarDelegateメッソドを使わない方法をいくつかやってみました。

結論はできませんでした。

やってみた方法は下の2つです。

1. searchBarのtextにKVOをかける
2. searchBarのテキストにdidSetを設定

上の方法ができなった理由はdUISearchBarDelegateメッソドと動作が完全い違いました。

2が呼ばれる場合は検索窓をタップした時、検索を実行した時。２つのケースでした。

さらにsearchControllerのUISearchBarDelegateメッソドでbreakPointを置いてコンソールで`po searchBar.text === self.searchController.searchBar.text`は`true`でしたが、`po searchBar.text`と`self.searchController.searchBar.text`違いました。

この問題が解決できたらUISearchBarDelegateとdelayを使わなくても実装ができると思います。

自分の場合は今のところでは解決できなくて、`UISearchBarDelegate`を活用しました。

もっといい方法があればぜひご連絡お願いします！

## まとめ
１つの言語に２種類のキーボードがある場合は日本語だけではないです。
他のキーボードでも悩みがありそうです！

ちなみに韓国発サービス（LINE, Kakaotalk等）では確定ボタンについて対応できてなかったんですが、他の日本発サービスでは対応できていました。

キーボード以外にも日本で開発する時、気をつける必要があるところシリーズを続きたいと思います。
