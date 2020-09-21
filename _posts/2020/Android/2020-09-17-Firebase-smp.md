---
title:  "푸시메시지 AtoZ"
excerpt: "파이어베이스,앱,FCM 그리고 Broadcast"
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - android
  - FCM
tag:
  - android
last_modified_at: 2020-09-17T20:00:00+01:00
---

<br>

# 0. 준비
## 1단계) 앱 세팅
비어있는 기본 안드로이드 파일을 생성해준다.<br>

## 2단계) 접속하기
<https://console.firebase.google.com/u/1/> <br>
이후 구글계정 로그인.
<br>

# 1. Firebase Console 설정 워크플로 사용하여 앱 등록

## 1단계) Firebase 프로젝트 만들기

Android 앱에 Firebase를 추가하려면 우선 Android 앱에 연결할 Firebase 프로젝트를 만들어야 한다.<br>
프로젝트 추가 > 순서에 따라 실행.

![1](/assets/images/firebase/1.png)

프로젝트가 이렇게 만들어지면 성공<br>

## 2단계) Firebase에 앱 등록

![2](/assets/images/firebase/2.png)
- 앱에 firebase를 추가한다.<br>

![3](/assets/images/firebase/3.png)
- 내 패키지 이름을 가져온다. 아래 그림 참고

![4](/assets/images/firebase/4.png)

## 3단계) 구성파일 다운로드

- 다운로드한 json파일을 내 프로젝트에 넣는다.

![5](/assets/images/firebase/5.png)
![6](/assets/images/firebase/6.png)
![7](/assets/images/firebase/7.png)

## 4단계) SDK 추가

![8](/assets/images/firebase/8.png)

- In Project Gradle<br>
![9](/assets/images/firebase/9.png)
![10](/assets/images/firebase/10.png)

- In App Gradle<br>
![11](/assets/images/firebase/11.png)

## 5단계) 설치확인

- 앱 실행 후 확인
![12](/assets/images/firebase/12.png)

## 6단계) FCM 설정

![13](/assets/images/firebase/13.png)
Sync now를 했을때 문제가 없다면 FCM을 사용하기 위한 설정이 완료되었습니다.
Firebase의 최신 버전이 다르다면 gradle 에러가 있을 수 있습니다. 그때는 에러메시지를 잘 읽고 최신버전으로 바꾸면 Sync 됩니다.

- Manifest에 추가 <br>
![14](/assets/images/firebase/14.png)

## 7단계) 코드추가 후 앱 실행
![15](/assets/images/firebase/15.png)
![16](/assets/images/firebase/16.png)
- onMessageReceived()<br>
Notification 구성에 필요한 요소들을 받아 처리하는 함수.<br>

[참고](https://popcorn16.tistory.com/69)
<br>

# 2. 메시지 전송

## 1단계) 링크
![17](/assets/images/firebase/17.png)

## 2단계 타이틀,내용 작성)
![18](/assets/images/firebase/18.png)

## 3단계 타겟팅 및 파라미터 설정)
![20](/assets/images/firebase/20.png)
![21](/assets/images/firebase/21.png)
![22](/assets/images/firebase/22.png)
![23](/assets/images/firebase/23.png)
![24](/assets/images/firebase/24.png)

이 때 Manifest설정 해줘야 함.<br>
![25](/assets/images/firebase/25.png)

## 4단계 post로 전송)
- postman쓰거나 chrome 또는 whale 확장앱에 advanced REST client 사용<br>
![26](/assets/images/firebase/26.png)
![27](/assets/images/firebase/27.png)
여기서 
- notification 부분은 onMessageReceived함수에서 받아 noti화면에 쓰이고,<br>
- data 파라미터 부분은 모바일에서 받아 사용이 가능하다.<br>
위 화면처럼 SubActivity.java로 보냈을 경우,<br>

```java
//in MainActivity.java
static String str = "";

private void registerReceiver() { // woww라는  BroadCastReceiver 생성 2)
        LocalBroadcastManager.getInstance(this)
                .registerReceiver(mBR, new IntentFilter("woww"));
}

BroadcastReceiver mBR = new BroadcastReceiver() { // 4)
    @Override
    public void onReceive(Context context, Intent intent) { 
        String msg = intent.getStringExtra("message"); // receive msg
        String cust = intent.getStringExtra("cust"); // receive cust
        Log.d("ttt",msg+cust);
        // textView.setText(msg+cust); // ui변경 안됨.
        str = msg+cust;
        textView.setText(str); // static으로 선언된 변수에 넣어주면 변경됨.

    }
};

...

// in SubActivity.java
Intent secondIntent = getIntent(); // postman으로부터 받은 intent 1)
String msg = secondIntent.getStringExtra("message");
String cust = secondIntent.getStringExtra("custom");

Intent intent = new Intent("woww"); //woww라는 Broacast에 msg,cust를 put. 3)
intent.putExtra("message",msg);
intent.putExtra("custom",cust);
LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
```

## 참고)
- post로 바로 보낼 때 서버토큰 api 키
![19](/assets/images/firebase/19.png)

<br>