---
layout: post
title:  "Kotlin으로 개발하는 안드로이드 프로젝트에서 Retrofit2 사용해보기"
date:   2019-11-28 00:22:16 +0900
categories: android
---

# Kotlin + Retrofit2

안녕하세요 ! 안하 🐧  ! (안드로이드 하이)

본업은 물론 iOS로 매일매일 Swift를 써가고 있습니다.
Flutter를 해볼까 했었는데 Android Studio 의 벽이 있더러구여 ㅠㅠ 양방향 플랫폼을 하려면 그래도 안드로이드에 대한 이해도 필요할 것 같기도해서 요즘 쪼금쪼금씩 안드로이드를 만져보고 있어요.

java 프로젝트에서 Retrofit2를 사용하는 블로그글은 많았지만, Kotlin 로 작성된 글은 얼마 없지않아서 저의 작은 경험을 공유해보고자 오랜만에 블로그 글을 적습니다 !

## Retrofit2?

https://square.github.io/retrofit/

외부 API 와 통신을 쉽게 할 수 있게 도와주는 Java 기반의 오픈소스 라이브러리입니다.
iOS의 Alamofire, AFNetworking 격인 라이브러리입니다.

## 개발환경

🧰 **Android Studio** v3.5
🖋 **Kotlin** v1.3.50
📱minSdkVersion 21

## 사용할 API

https://swapi.co/

스타워즈 API 를 사용하는 전제
~키 등 인증을 신경쓰지않아도 되는 API 입니다~

## 실제로 사용해보기

### 1. 프로젝트 내 설정

1. Project 내 `build.gradle`의 `dependencies`에  Retrofit2 를 추가
    - 사용하실 버전을 꼭 확인해주세요! 저는 사용할 당시의 최신 버전으로 설정했습니다.
        - [🔗 Retrofit GitHub repository](https://github.com/square/retrofit)

```gradle
dependencies {
    implementation "com.squareup.retrofit2:retrofit:2.6.0"
    implementation "com.squareup.retrofit2:converter-gson:2.6.0"
}
```

2. `AndroidManifest.xml`에서 작업하실 안드로이드 프로젝트에 인터넷 권한을 추가해주세요.

    - iOS 에서는 별도로 네트워킹 권한 설정이 없어서 자주 까먹을 것 같아요 😂

```xml
..
...

    <uses-permission android:name="android.permission.INTERNET"></uses-permission>
</manifest>
```

### 2. Retrofit2을 사용해서 API 통신하기

> 이번 글에서는 간단한 API를 **get** 만 해봅니다.
> ~post 는 firebase 이외에 간단하게 해볼 수 있는 API 를 아시는 분 있으시면 꼭 알려주세요 !!! 🙇~

1. API 통신을 위해 모델 만들기.

```kt
// People.kt

data class People (
    val name: String,
    val height: Int,
    val mass: Int,
    val hairColor: String,
    val skinColor: String,
    val eye_color: String,
    val birthYear: String,
    val gender: String,
    val homeworld: URL
)
```

2. API 통신으로 성공된 값을 화면에 표시하기 위한 textView를 설정하기
    - `MainActivity.xml`에 `textView`를 추가해주었어요 !

3. API 통신 코드 추가하기

```kt
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        request()
    }

    private fun request() {
        val baseURL = "https://swapi.co/api/"

        val retrofit = Retrofit
            .Builder()
            .baseUrl(baseURL)
            .addConverterFactory(GsonConverterFactory.create())
            .build()

        val service = retrofit.create(PeopleService::class.java)

        service.getPeople().enqueue(object: Callback<People> {
            override fun onResponse(call: Call<People>, response: Response<People>) {
                textView.text = "성공 😇😇😇😇" + response.body()?.name
            }

            override fun onFailure(call: Call<People>, t: Throwable) {
                textView.text = "request 에 실패하였습니다."
                // 에러 메세지를 표시하는 Toast 를 추가하는 등의 에러 처리를 추가하는 것도 가능 !
            }
        }
        )
    }
}

interface PeopleService {
    @GET("people/1")
    fun getPeople(): Call<People>
}
```

- ~`interface` 의 정의가 어떻게 바로 retrofit에서 컴파일이 되는건지는 아직 오리무중......... 😵~

3. 통신이 되었는지 확인하기

    <img src="https://user-images.githubusercontent.com/28520053/69733712-205a8000-1171-11ea-8fdb-262d059567e9.png" alt="" width="200"/>

    - 🎉

---

## 마무리

사실 안드로이드에서 통신을 해본다는 것. API를 사용해본다는 것 자체를 해보고 싶었던 욕심에
처음에는 MVP처럼 아키텍쳐를 나누어봤지만 왜인지 잘 되지않아서 사실 View와 네트워킹 코드가 거의 구분되지 못했어요 😢

물론 View가 많아지고 복잡하면 리퀘스트 타이밍에 따라 버그가 발생하기도 하고 여러가지 신경써줘야할 일이 늘어날 것이라 생각합니다.

프로젝트 자체는 스타워즈의 출연진(?) 캐릭터(?)의 API를 사용해서 get하는 것밖에없지만,
개인적으로는 여기서 다양한 아키텍쳐와 테스트 등을 직접 체험해보려합니다.

부족한 글 읽어주셔서 감사합니다.

~👷 혹시 블로그 글에 대한 보충이나 피드백, 잘못된 내용이 있다면 ! 알려주세요 !~
