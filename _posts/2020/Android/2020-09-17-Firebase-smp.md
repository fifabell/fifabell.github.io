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

## 7단계) 코드추가 후 앱 실행 (기본)
![15](/assets/images/firebase/15.png)
![16](/assets/images/firebase/16.png)
- onMessageReceived()<br>
Notification 구성에 필요한 요소들을 받아 처리하는 함수.<br>

[참고](https://popcorn16.tistory.com/69)
<br>

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

## 실습 POST
```javascript
{
  "priority": "high",
  "data": {
    "title": "타이틀!",
    "content": "내용@!~",
    "gubun": "SUB",
    "message":"메시지..."
  },
  "registration_ids": [
    "edUeKnGoTXikp_1BaGaG5Z:APA91bH3s25L_l..." // 키 여러개 보낼 수 있음.
  ]
}
```

<br>

# 3. BroadcastReceiver

## 1) 선언

`BaseActivity.java`
```java
//oncreate
registerReceiver();

// out of oncreate
//브로드캐스트 리시버 등록
private void registerReceiver() {
    LocalBroadcastManager.getInstance(this)
            .registerReceiver(mReceiver, new IntentFilter("woww"));
}

// 알림 수신 브로드캐스트 리시버
BroadcastReceiver mReceiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        mMsg = intent.getStringExtra("inBroadMessage");
    }
};

```

## 2) 4-2 참고 

## 3) 값 받기

4 진행 후 오면 됨.
`MainActivity.java`
```java
public class MainActivity extends BaseActivity {
//mMsg 쓰면 됨
//BaseActivity 에 전역함수 설정되어있음. static String mMsg;
```

`SubActivity.java`
```java
public class SubActivity extends BaseActivity {
//mMsg 쓰면 됨
```

<br>

# 4. 알림창 띄우기

## 0) push에서 받아오기

위에 코드는 기본 코드고, 이 아래는 알람을 띄우기 위한 코드들임
`MyFirebaseMessagingService.java`
```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {

...

@Override
public void onMessageReceived(RemoteMessage remoteMessage) {

    //푸시울렸을때 화면깨우기.
    PowerManager pm = (PowerManager) getSystemService(Context.POWER_SERVICE);
    @SuppressLint("InvalidWakeLockTag") PowerManager.WakeLock wakeLock = pm.newWakeLock(PowerManager.SCREEN_DIM_WAKE_LOCK
            | PowerManager.ACQUIRE_CAUSES_WAKEUP, "TAG");
    wakeLock.acquire(3000);

    if (remoteMessage.getData() == null)
        return;

    Alertmanager alertManager = Alertmanager.getInstance(getApplicationContext(), remoteMessage);
    alertManager.alert();
}
```

`Alertmanager.java`
```java
public class Alertmanager extends Context {

  // 선언
  protected static final String KEY_HOME = "MAIN";
  protected static final String KEY_SUB= "SUB";

  Context mContext;
  RemoteMessage mMsg;
  String gubun;
  private static Alertmanager mInstance;
  private final int NOTIFY_ID = 0;
  // 선언 end

  // MyFirebaseMessagingService 에서 getInstance 사용
  public static Alertmanager getInstance(Context applicationContext, RemoteMessage remoteMessage) {
      if (mInstance == null)
          mInstance = new Alertmanager();

      mInstance.init(applicationContext, remoteMessage);
      return mInstance;
  }

  private void init(Context applicationContext, RemoteMessage remoteMessage) {
      this.mContext = applicationContext;
      this.mMsg = remoteMessage;
  }

  // MyFirebaseMessagingService 에서 alert 사용
  public void alert() {
      // 푸시로부터 얻은 데이터 구분자로 사용
      gubun = parseKey(mMsg); // parseKey함수 참고

      // 현재 디바이스의 잠금상태를 확인하기위한 인스턴스 획득
      KeyguardManager km = (KeyguardManager) mContext.getSystemService(Context.KEYGUARD_SERVICE);
      if (km.isKeyguardLocked()) {  // 잠금상태일 경우
          // AlertActivity 호출
          startActivity(mMsg); // 여기 startActivity는 정의된 함수. 바로 아래 참고.
      } else {                      // 잠금상태가 아닐 경우
          // Notification 호출
          pushNotification();
      }
  }

  // key의 구분값을 가져와 return
  private String parseKey(RemoteMessage remoteMessage) {
      if (remoteMessage.getData().get(KEY_HOME) != null) {
          return KEY_SUB;
      } else {
          return KEY_HOME;
      }
  }

  private void startActivity(RemoteMessage msg) {
      Intent intent = getIntent(msg);
      if (intent != null) {
          mContext.startActivity(intent); // 여기 startActivity는 실제 intent구동 
      }
  }

  private Intent getIntent(final RemoteMessage msg) {
      Intent intent = null;
      if (msg != null) {
          intent = new Intent(mContext, AlertActivity.class); // AlertActivity에서 알림창 띄울 것.
          intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
          intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

          // HTTP request로 부터 받아서 구분자별 보내기
          intent.putExtra("gubun",msg.getData().get("gubun"));
          intent.putExtra("title",msg.getData().get("title"));
          intent.putExtra("content",msg.getData().get("content"));
          intent.putExtra("message",msg.getData().get("message"));

      }
      return intent;
  }

  private void pushNotification() {
      PendingIntent pIntent = getPendingIntent(mMsg); // intent 어디로 갈건지 참고. 아래 함수 참조.
      if (pIntent != null) {
          NotificationManager notificationManager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);

          Notification.Builder notiBuilder;

          if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { 
              // 오레오버전 이상일 땐 NotificationChannel을 만들어줘야 함. 밖에 들어나지 않는 설정이라고 보면 됨.
              NotificationChannel notificationChannel = new NotificationChannel("fifabell_NOTI","노티 알림", NotificationManager.IMPORTANCE_DEFAULT);
              notificationChannel.setDescription("channel 상세설명");
              notificationChannel.enableLights(true);
              notificationChannel.setLightColor(Color.GREEN);
              notificationChannel.enableVibration(true);
              notificationChannel.setVibrationPattern(new long[]{100, 200, 100, 200});
              notificationChannel.setLockscreenVisibility(Notification.VISIBILITY_PRIVATE);
              notificationManager.createNotificationChannel(notificationChannel);
              notiBuilder = new Notification.Builder(mContext, "fifabell_NOTI");
          } else {
              // 오레오버전 미만일 땐 그냥 빌드.
              notiBuilder = new Notification.Builder(mContext);
          }
          // 여기서 Notification 뷰를 만듬
          notiBuilder.setSmallIcon(R.mipmap.ic_launcher)
                  .setWhen(System.currentTimeMillis())
                  .setContentTitle(mMsg.getData().get("title"))
                  .setContentText(mMsg.getData().get("content"))
                  .setContentIntent(pIntent)
                  .setAutoCancel(true)
                  .setPriority(Notification.PRIORITY_MAX)
                  .setDefaults(Notification.DEFAULT_ALL);

          notificationManager.notify(NOTIFY_ID, notiBuilder.build()); // notify를 해줘야 구동됨.
      }
  }

  // where to go
  private PendingIntent getPendingIntent(RemoteMessage mMsg) {
      PendingIntent pendingIntent;
      Intent intent;
      if (mMsg.getData().get("gubun").equals("SUB")) { // 구분이 SUB이면 SubActivity로 감
          Log.d("getPendingIntent","gubun: SUB");
          intent = new Intent(mContext,SubActivity.class);
      } else {                                        // 구분이 그 외엔 MainActivity로 감
          Log.d("getPendingIntent","gubun: MAIN");
          intent = new Intent(mContext, MainActivity.class);
      }

      intent.putExtra("NOTIFICATION", NOTIFY_ID);
      intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);

      pendingIntent = PendingIntent.getActivity(mContext,0,intent, PendingIntent.FLAG_CANCEL_CURRENT);
      return pendingIntent;
  }

...

수많은 오버라이드...처리해주면 됨.

```

## 1) Alert

in Alertmanager.java > startActivity(mMsg); > getIntent(msg); > AlertActivity.class intent

manifest에 추가<br>

```javascript
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

`AlertActivity.java`
```java
public class AlertActivity extends BaseActivity {
  private String mTitleText;
  private String mContentsText;
  private String mGubun;
  private String mMessage;

  Intent intent;

      @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE);

        // 잠금화면위에 액티비티를 띄우기 위한 플래그
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_SHOW_WHEN_LOCKED);

        // 풀 스크린 플래그
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);

        ActionBar actionBar = getSupportActionBar();
        actionBar.hide();

        setContentView(R.layout.activity_alert);

        initData(getIntent()); // 초기화 : 데이터를 받아와서 broadcastReceiver에 넣어줌.
        initComponent(); // alert창에 title, content 달아줌.

        Button open_btn = findViewById(R.id.button);
        // 확인버튼 클릭 시 where to go
        open_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (mGubun.equals("SUB")) {
                    intent = new Intent(AlertActivity.this,SubActivity.class);
                } else {
                    intent = new Intent(AlertActivity.this, MainActivity.class);
                }
                startActivity(intent);
                finish();
            }
        });

        // 화면 꺼질때 broadcastReceiver내용 삭제
        IntentFilter filter = new IntentFilter();
        filter.addAction("android.intent.action.SCREEN_OFF");
        registerReceiver(mPowerBroadcast, filter);

        /*
         * 알람이 울릴시 진동주기
         * */

        long[] pattern = {0, 500, 200, 400, 100};
        Vibrator vibe = (Vibrator) getSystemService(Context.VIBRATOR_SERVICE);
        // pattern 을 진동의 패턴 -1은 패턴의 반복은 한번
        vibe.vibrate(pattern, -1);
    }

    private void initData(Intent intent) {
        // 값 받아오기.
        mTitleText = intent.getStringExtra("title");
        mContentsText = intent.getStringExtra("content");
        mGubun = intent.getStringExtra("gubun");
        mMessage = intent.getStringExtra("message");

        // woww라는 broadcast안에 값 넣어줌
        Intent broadIntent = new Intent("woww");
        broadIntent.putExtra("inBroadMessage",mMessage);
        LocalBroadcastManager.getInstance(this).sendBroadcast(broadIntent);
    }

    TextView mTitle;
    TextView mContents;
    private void initComponent() {
        mTitle = (TextView) findViewById(R.id.alert_title);
        mContents = (TextView) findViewById(R.id.alert_contents);
        mTitle.setText(mTitleText);
        mContents.setText(mContentsText);
    }

    BroadcastReceiver mPowerBroadcast = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (intent.getAction().equals("android.intent.action.SCREEN_OFF")) {

                // 브로드캐스트 리시버 제거
                unregisterReceiver(mPowerBroadcast);

                //액티비티 종료
                AlertActivity.this.finish();
            }
        }
    };

```

## 2) Notification

Alertmanager > pushNotification() 부분 보면 됨.

<br>

# * 참고 *
- post로 바로 보낼 때 서버토큰 api 키
![19](/assets/images/firebase/19.png)

- 화면 꺠우기 코드 

```java
//푸시울렸을때 화면깨우기.
PowerManager pm = (PowerManager) getSystemService(Context.POWER_SERVICE);
@SuppressLint("InvalidWakeLockTag") PowerManager.WakeLock wakeLock = pm.newWakeLock(PowerManager.SCREEN_DIM_WAKE_LOCK
        | PowerManager.ACQUIRE_CAUSES_WAKEUP, "TAG");
wakeLock.acquire(3000);


// 알람이 울릴시 진동주기
long[] pattern = {0, 500, 200, 400, 100};
Vibrator vibe = (Vibrator) getSystemService(Context.VIBRATOR_SERVICE);
// pattern 을 진동의 패턴 -1은 패턴의 반복은 한번
vibe.vibrate(pattern, -1);
```
<br>