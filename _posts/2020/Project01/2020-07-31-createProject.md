---
title:  "[3] spring boot 테스트 프로젝트 생성"
excerpt: "구성된 서버에 프로젝트를 실행해본다."
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - Project01
tag:
  - controller
  - spring boot

last_modified_at: 2020-07-30T20:00:00-24:00
---

# spring boot
> spring boot는 spring 프레임워크를 시작하기 쉬운 초기화라고 생각하면 된다.
> setting이 기본적으로 다 잘 되어있음.

## 1) spring init ! 
[startSpringboot](https://start.spring.io/)
링크로 들어가서 세팅을 다음과 같이 한다.

![startSpringboot](/assets/images/prj01_spinit.png)

project open > src > main > java파일 실행 시 <br>
TomcatServer가 열리면서 (localhost://8080) 접속이 가능해진다.

![sprbootStart](/assets/images/prj01_sprbootStart.png)

위와 같은 페이지가 뜨면 성공 !

## controller를 이용해 url mapping

### ① localhost:3030 접속 시 
`TestController.java`
```java
  @RestController
  public class TestController {
      @RequestMapping("/")
      public String index() {
          return "Hello";
      }
  }
```
RequestMapping Annotation을 이용해서 root로 접속 시 Hello를 리턴하는 걸 알 수 있다.

▶︎ 출력
```
Hello
```

### ② localhost:3030/test/member 접속 시 
`MemberController.java`
```java
  @RequestMapping("/member")
  @RestController
  public class MemberController {
      
      @GetMapping("/test")
      public ArrayList<Member> memberTest(){
          return new ArrayList(Arrays.asList(
                  new Member("Wickies", 20),
                  new Member("철수", 30),
                  new Member("영희", 25)
          ));
      }
  }
```

RequestMapping, getMapping을 사용하여 <br>
/member/test 접속 시 mapping 해줌.<br>
이 때, MemberClass의 객체를 생성하면서 array로 return한다.
<br>

`Member.java`
```java
public class Member {
    private String name;
    private int age;

    public Member(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return name;
    }

    public int age() {
        return age;
    }

    public void setName(String name){
        this.name = name;
    }

    public void setAge(int age){
        this.age = age;
    }
}
```

객체 생성 시 생성자 / getter / setter는 써야 하지만, <br>
*lombok*으로 대체할 수 있다.(getter,setter를 생략할 수 있다.)

▶︎ 출력
```
[{"name":"Wickies"},{"name":"철수"},{"name":"영희"}]
```

이어서 다음 포스트에는 이 페이지를 내 ubuntu 서버로 옮기는 포스트가 계속된다.



# # 참조 사이트

- [spring boot 참조 블로그](https://wickies.tistory.com/99?category=768093)