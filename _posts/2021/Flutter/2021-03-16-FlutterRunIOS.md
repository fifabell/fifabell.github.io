---
title:  "Flutter run in IOS."
excerpt: "좌충우돌 ios 실 기기 테스트"
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - Flutter
tag:
  - Flutter
  - ios
  - mac
last_modified_at: 2021-03-16T20:00:00+01:00
---

<br>

## 0) 버전

> flutter 2.0.1
 

## 1) 기본 방향

> 기본은 [Flutter 홈페이지 guide][https://flutter-ko.dev/docs/get-started/install/macos]에 따라 진행했다.

필자는 window에서 flutter 실행과 android기기 테스트만 진행하였기에, 이번 기회를 통해 ios에서 run을 해보기로 했다.<br>

### 1. sdk download
홈페이지에서 zip파일을 받아 unzip명령어를 통해 압축을 풀기.

```shell
 cd ~/development
 unzip ~/Downloads/flutter_macos_2.0.1-stable.zip
```

### 2. export 추가
아마 window에서 환경변수 설정이랑 동급이라고 판단된다.<br>

```shell
 export PATH="$PATH:`pwd`/flutter/bin"
```

이렇게하면 된다고 했지만, 되지 않아서 다른 방법을 선택했다.

```shell
 'in bash'
 vi ~/.bash_profile

 'in vim'
 export PATH="$PATH:/Users/Your_name/`pwd`/flutter/bin"
```
마찬가지로 환경변수를 등록하는건데, 여기서 하면 cmd를 다시 실행할 때 마다 

```shell
  source ~/.bash_profile
```

명령어를 실행해줘야 한다.

하지만, 필자는 이 방법이 잘 됐기에 이걸로 사용중이다. <br>
아마 처음 시도할 때 첫 번째 방법에 pwd(내 경로)를 잘 찾지 못해서 생긴 경우일 것 같다.<br>
* source 명령어 없이 계속 사용 가능한 방법 있으면 좀 알려주세요..<br>
(프로젝트 생성과정 생략)

### 3.flutter doctor
기본적으로 flutter를 지금 환경에서 구동할 수 있는지 doctor가 점검해준다.(ㅋㅋ)<br>

`flutter doctor -v` 명령어를 사용하면 더 자세히 알려준다.<br>
x표시가 뜨는 건 고쳐주고, !표시 정도는 넘어가도록 하자.<br>

### 4.flutter run
사실 이게 단데, 여기서부터가 본격적으로 문제다.<br>
flutter run은 기본적인 flutter 실행 명령어다.<br>
하지만, mac과 ios는 쉽게 허락하지 않는다.<br>

finder에서 본인 flutter /ios/Runner.xcworkspace 라는 파일이 보일것이다.<br>
더블클릭하면 xCode가 실행될 것이다.<br>

참고로 필자는 아직도 xcode에서 실행은 안된다. (이유는 자꾸 찾아봐도 모르겠음/ 알려주세요)<br>

제일 좌측상단에 파일같이 생긴 아이콘을 클릭해보면, 현재 열려있는 프로젝트가 나올것이고, 가장 상단에 Runner를 클릭하면, 기본적인 설정값들을 제어할 수 있다.<br>

- 우선 vscode에서 command+shift+f로 com.example를 검색해 다른 본인만의 identity가 있는 이름으로 변경해주고, 다시 xcode에서 2번째 탭 > Bundle Identifier이 바뀌는지 확인해보자.안되어있으면 마찬가지로 변경

- 2번째 탭 Signing & Capablities로 들어가서, Team에 본인 계정을 선택하자(유료아니여도됌)

- 5번째 탭 Build Settings에 All/Combined를 선택하고, Architecutures > Build Active Architecture Only > Release > Yes (이건 안해줘도 될듯?)

- 동일 탭에서 All/Combined Signing > Code Signing Identity > Realease > Any IOS SDK : iOS Developer로 변경(옛날 구글자료는 Apple Distribution으로 하라던데 난 그걸로하면 안됌.)

### does not contain an xcode project
실행하니 이런 에러와 마주했다.

```shell
  xattr -cr /Users/my_flutter_root 
```
아마 xcode 클린시켜주는 코드인듯?

### ios는 debug모드를 수행하지 않는다.
실기기에서 debug모드가 안됨.

```shell
  flutter run --release
```
이 명령어를 통해 릴리즈 버전을 실행시켜주면 됌

### behind
하도 삽질을 많이 해서 뭐가 더 있었던 것 같은데, 기억도 잘 안나네 ..
최대한 정리해봄. 바이 ㅡ!
