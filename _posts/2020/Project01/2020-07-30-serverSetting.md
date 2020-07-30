---
title:  "[2] 서버환경구축"
excerpt: "서버환경을 어떻게 구축하는 지 알려준다."
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - Project01
tag:
  - 서버환경구축
  - server
  - aws
last_modified_at: 2020-07-30T18:00:00-20:00
---

# 1. 서버환경구축

웹 프로젝트에서 서버환경구축은 기본이자 시작이다.

# 2. step by step

## 1) 하드웨어(Hardware)

서버 구축에도 다양한 하드웨어가 사용된다.<br>
라즈베리파이 같은 작은 기기부터 개인 노트북이나 가정용 pc나 별도의 서버 pc를 구매해도 좋지만,<br>
amazon에서 제공하는 서버로 구축해보도록 하겠다.

## 2) aws

aws(Amazon Web Services)에 접속하면 `EC2`라는 서버구축 가능한 ui가 있다.<br>
기본적으로 해당 프로젝트는 따끈따끈한 `ubuntu 20.04`를 사용하도록 하겠다. <br>
aws 내의 기타 보안 설정 및 ssh 접속방법은 생략한다.

# 3. webServer & WAS

## 1) java설치

- 패키지 관리자 업데이트
```
sudo apt-get update
```

- 버전 확인
```
java --version
```
명령어 사용 시 command를 알아차리지 못하면 설치가 안된 것.

- 14v jdk 설치 
```
sudo apt-get install openjdk-14-jdk
```

다시 java 버전확인해보면 14버전이 잘 설치된 걸 확인할 수 있다.

## 2) tomcat9 설치 (Web Application Server, 동적)
> _spring boot에 내장되어있음. 필요 시 별도 설치_

- tomcat 9 설치 (10은 알파버전)
```
sudo apt-get install tomcat9
```

- 톰캣설치완료 후 버전체크
```
sudo systemctl status tomcat9
```
하게되면

```
no JDK or JRE found - please set JAVA_HOME
```
이런 에러를 접하게 됌.

- JAVA_HOME 설정
우선 javac(java컴파일러) 위치를 확인
```
which javac
> /usr/bin/javac
```
```
readlink -f /usr/bin/javac
> usr/lib/jvm/java-14-openjdk-amd64/bin/javac
```
javac의 실제 위치는 usr/lib/jvm/java-14-openjdk-amd64/bin/
JAVA_HOME을 /usr/lib/jvm/java-14-openjdk-amd64 으로 설정해주면 됌.
```
sudo vi /etc/profile
```
profile 제일 하단에 
```
export JAVA_HOME=/usr/lib/jvm/java-14-openjdk-amd64
```
추가

- tomcat9에서 JAVA_HOME 설정 (주석풀고 수정)
```
JAVA_HOME=/usr/lib/jvm/java-14-openjdk-amd64/bin
```

- 확인
ssh 재접속

- echo로 확인
```php
echo $JAVA_HOME
> /usr/lib/jvm/java-14-openjdk-amd64
```
```php
$JAVA_HOME/bin/javac -version
> javac 14.0.1
```

- 웹 서버 작동
```
sudo systemctl start tomcat9
```
```
sudo systemctl status tomcat9
```

- 접속
Tomcat 기본 webserver는 8080이므로
ip주소:8080으로 접속하면 정상적으로 작동하는 걸 알 수 있다.



## 3) apache2 (Webserver, 정적)
> _더 나은 성능과 기능을 위해 apache를 웹서버로 사용하는 게 더 낫다._

- apache2 설치
```
sudo apt-get install apache2
```
```
sudo service apache2 start
```

- 접속
apache2 기본 webserver는 80이므로
ip주소:80으로 접속하면 정상적으로 작동하는 걸 알 수 있다.




# # 참조 사이트

- [Ubuntu20.04 & webServer 참조 블로그](https://kibua20.tistory.com/82)