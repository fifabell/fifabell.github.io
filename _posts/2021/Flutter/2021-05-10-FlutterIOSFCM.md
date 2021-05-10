---
title:  "Flutter firebase_messaging 9 in IOS."
excerpt: "IOS FCM 테스트"
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - Flutter
tag:
  - Flutter
  - ios
  - fcm
last_modified_at: 2021-03-16T20:00:00+01:00
---

<br>

## 0) 버전

> flutter 2.0.1 <br>
> firebase_messaging: 9.1.0 <br>
> firebase_core: 1.0.3 <br>
> flutter_local_notifications: 5.0.0+3 <br>


## 1) 기본 방향
- 기본 세팅은 android studio로 진행,
- 세부 코딩은 vscode로 진행함.

- ios 간단 코드 변경 시 저장만 하면 되지만, 세팅의 경우 flutter root 경로에서 아래 코드를 통해 업데이트.

```shell
cd ios
pod repo update 
```

## 2) 세팅
1. 패키지 주소 확인
- xcode에서 실행 (root/ios/Runner.Runnerxcworkspace)
- 왼쪽 TARGETS > Runner > Bundle Identifier 칸에 패키지 주소가 있음.

- 추가작업 : Signing에 Team 설정도 해줘야 함. 

2. Firebase에서 ios 프로젝트 생성 / 세팅 (타 블로그에 많음)
- xcode를 열었을 때, GoogleService-info.plist가 추가 안되있을 경우,
Runner프로젝트 내 Runner폴더 우클릭 > Add Files to "Runner"를 클릭해서 해당 파일을 추가해줘야 함.

3. 애플 개발자 세팅
- 개발자 만들기 (법인의 경우 승인까지 기간이 하루정도 소요됌.)
- [TEAM ID](https://developer.apple.com/account/#/membership/)

- [Identifiers](https://developer.apple.com/account/resources/identifiers/list)
- 추가 -> App IDS -> continue
- App ID는 TEAM ID를 넣어주고, Capabilities는 Push Notification

- [Certificates](https://developer.apple.com/account/resources/certificates/list)
- 추가 -> Apple Push Notification service SSL (Sandbox & Production)
- 맥북의 키체인 접근 실행 -> 인증서 지원 -> 인증기관에서 인증서 요청... -> 사용자 메일과 이름 입력 후 계속
- 저장 후 해당 파일 Choose File 업로드
- 생성된 인증서를 다운로드 후 더블클릭하여 실행. (인증서 추가됌)

- [AuthKey](https://developer.apple.com/account/resources/authkeys/list)
- Apple Push Notification service (APNs) 선택 후 Continue
- Download (1회밖에 발급안됨 / 재발급 필요 시 다시 키 생성)

- 키 업로드 
- Firebase -> 톱니바퀴 -> 프로젝트 설정 -> 클라우드 메시징 -> apn 인증 키 -> 방금 받은 키 업로드

4. Flutter 작업
- firebase_messaging 9.이상 패키지 추가
- 기본 코드

```dart
import 'package:firebase_core/firebase_core.dart';
import 'package:firebase_messaging/firebase_messaging.dart';
import 'package:flutter/material.dart';
import 'package:flutter_local_notifications/flutter_local_notifications.dart';

Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
  // 백그라운드에서 ...
  await Firebase.initializeApp();
  print('in background');
  print(
      'Handling a background message notification ${message.notification.title}');
  print(
      'Handling a background message notification ${message.notification.body}');
  print(
      'Handling a background message notification ${message.notification.android.clickAction}');
  print(
      'Handling a background message notification ${message.notification.apple}');
}

/// Create a [AndroidNotificationChannel] for heads up notifications
const AndroidNotificationChannel channel = AndroidNotificationChannel(
  'high_importance_channel', // id
  'High Importance Notifications', // title
  'This channel is used for important notifications.', // description
  importance: Importance.high,
);

/// Initialize the [FlutterLocalNotificationsPlugin] package.
final FlutterLocalNotificationsPlugin flutterLocalNotificationsPlugin =
    FlutterLocalNotificationsPlugin();

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  // init
  await Firebase.initializeApp();

  // firebasemessaging & handler
  FirebaseMessaging.onBackgroundMessage(_firebaseMessagingBackgroundHandler);

  // plugin and channel 
  await flutterLocalNotificationsPlugin
      .resolvePlatformSpecificImplementation<
          AndroidFlutterLocalNotificationsPlugin>()
      ?.createNotificationChannel(channel);

  // foreground options
  await FirebaseMessaging.instance.setForegroundNotificationPresentationOptions(
    alert: true,
    badge: true,
    sound: true,
  );

  runApp(TestApp());
}

// ignore: camel_case_types
class TestApp extends StatefulWidget {
  @override
  _TestAppState createState() => _TestAppState();
}

// ignore: camel_case_types
class _TestAppState extends State<TestApp> {
  @override
  void initState() {
    super.initState();
    FirebaseMessaging.instance.getInitialMessage().then((message) async {
      // 앱이 꺼져있는  상태에서 노티로 클릭하여 열기
      print('app-off-noti-click');
      if (message != null) {
        await print('app off... messageTitle:${message.notification.title}');
        await print('app off... messageBody:${message.notification.body}');
      }
    });
    FirebaseMessaging.onMessage.listen((message) {
      // 앱이 켜져있을 때

      print('app-on');
      var notification = message.notification;
      var android = message.notification?.android;

      // if (notification != null && android != null) {
        // android checking
        if (notification != null) { // any OS
        print("notification.hashCode:${notification.hashCode}");
        print("notification.title:${notification.title}");
        print("notification.body:${notification.body}");
        print("channel.id:${channel.id}");
        print("channel.name:${channel.name}");
        print("channel.description:${channel.description}");
      }
    });

    FirebaseMessaging.onMessageOpenedApp.listen((message) {
      // 백그라운드 상태에서 노티로 클릭하여 열기
      print('from-background-noti-click');
      print("open App and messageTitle:${message.notification.title}");
      print("open App and messageBody:${message.notification.body}");
      print('A new onMessageOpenedApp event was published!');
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Naviagtor',
        initialRoute: '/route',
        routes: {
          '/route': (context) => Main(), // 메인화면
        },
        onUnknownRoute: (settings) {
          return MaterialPageRoute(
            builder: (_) => NotFoundScreen(),
          );
        },
      ),
  }
}

```

5. 토큰 값 불러오기

```dart
var token;
WidgetsFlutterBinding.ensureInitialized();
await Firebase.initializeApp();

print("getToken in AnyOS");
token = await FirebaseMessaging.instance.getToken();
// Example보면 ios는 getAPNSToken가 있지만, APNSToken으로는 fcm이 안날아감.
```

6. fcm세팅 끝. fcm 테스트는 타 블로그를 참고하시면 됩니다 !
