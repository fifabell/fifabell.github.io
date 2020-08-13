---
title:  "Interpreter Pattern"
excerpt: "인터프리터 패턴에 관한 설명입니다."
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - DesignPattern
tag:
  - Interpreter Pattern
last_modified_at: 2020-08-12T21:00:00+03:00
---
<br>

# 1. 인터프리터 패턴(Interpreter Pattern)이란? 

Interpreter의 사전적 해석 : __통역사__ <br>

> `문장을 해석`할 때 사용하는 패턴. <br>
> 해석기, 즉 한 언어에서 문들을 평가하는 방법을 규정하는 디자인 패턴이다.

<br>

---

# 2. 인터프리터 패턴의 역할

## 1) UML

![interpreterUML](/assets/images/interpreterUML.png)


추상적인 표현을 client가 구문을 이용하여 구체적인 표현들을 정의한다.

<br>

## 2) 역할

간단한  쉘커먼드 처리, 통신 프로토콜처리, 수식계산, 도메인 기반 언어(Domain Specific Language - SQL, XML, JSF등)의 해석에 주로 많이 쓰인다. <br>
일반프로그래밍 언어처럼 문장구성이 복잡해지면 사용하지 않는 것이 낫다.<br>

ex ) 2 add 3 <br>
피연산자 : 2,3 / 연산자 : + <br>
위와 같이 규정을 통해 구문 해석이 가능하다.

<br>
---

# 3. 문제

## 1) 문제
and, or을 이용하여 interpreter pattern을 구현하고자 한다.<br>
① Rule: "Robert" and "John" are male (Or)<br>
② Rule: "Julie" is a "married" women (And)<br><br>
 
Expression 인터페이스를 이용해<br>
TerminalExpression, AndExpression, OrExpression 클래스를 재정의하고,<br>
AndExpression("julie","married")객체와,<br>
OrExpression("Robert","John")객체를 활용하여<br>
Expression객체(isMale,isMarriedWoman)를 생성하고 함수를 생성해보자.

## 2) UML
![UML](/assets/images/interpreter_pattern_uml.jpg)

## 3) 역할
위 UML를 참조하여 아래 다섯 개의 클래스 및 인터페이스를 구현해보아라.

```
● Expression 인터페이스 - context 매개변수를 포함하는 boolean type의 interpret 함수를 갖고있다.
아래 3개의 클래스는 Expression인터페이스를 재정의하는 클래스
● TerminalExpression 클래스 - data 매개변수를 이용하여 생성자로 초기화한다.
● AndExpression 클래스 - interpret함수 재정의시 생성자에 의해 초기화한 두 매개변수를 interpret 함수안에서 and 연산과정을 return 해준다.
● OrExpression 클래스 - interpret함수 재정의시 생성자에 의해 초기화한 두 매개변수를 interpret 함수안에서 or 연산과정을 return 해준다.
● Main 클래스 - 
① TerminalExpression,OrExpression클래스를 이용해 "Robert","John"를 Expression 형으로 객체를 생성한다.
② TerminalExpression,AndExpression클래스를 이용해 "julie","married"를 Expression 형으로 객체를 생성한다.

```

## 4) 해답

`Expression.java`
```java
public interface Expression {
    public boolean interpret(String context);
}
```
<br>

`TerminalExpression.java`
```java
public class TerminalExpression implements Expression {

    private String data;

    public TerminalExpression(String data) {
        this.data = data;
    }

    @Override
    public boolean interpret(String context) {
        
        if(context.contains(data)){
            return true;
        }
        return false;
    }
}
```
<br>

`OrExpression.java`
```java
public class OrExpression implements Expression {

    private Expression expr1 = null;
    private Expression expr2 = null;

    public OrExpression(Expression expr1, Expression expr2){
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    @Override
    public boolean interpret(String context) {
        return expr1.interpret(context) || expr2.interpret(context);
    }
}
```
<br>

`AndExpression.java`
```java
public class AndExpression implements Expression {

    private Expression expr1 = null;
    private Expression expr2 = null;

    public AndExpression(Expression expr1, Expression expr2){
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    @Override
    public boolean interpret(String context) {
        return expr1.interpret(context) && expr2.interpret(context);
    }
}
```
<br>

`InterpreterPatternDemo.java`(main)
```java
public class InterpreterPatternDemo {
    // Rule: Robert and John are male
    public static Expression getMaleExpression(){
        Expression robert = new TerminalExpression("Robert");
        Expression john = new TerminalExpression("John");
        return new OrExpression(robert, john);
    }

    // Rule: Julie is a married women
    public static Expression getMarriedWomanExpression(){
        Expression julie = new TerminalExpression("Julie");
        Expression married = new TerminalExpression("Married");
        return new AndExpression(julie, married);
    }


    public static void main(String[] args) {
        Expression isMale = getMaleExpression();
        Expression isMarriedWoman = getMarriedWomanExpression();

        System.out.println("John is male? " + isMale.interpret("John"));
        System.out.println("Julie is a married women? " + isMarriedWoman.interpret("Married Julie"));
    }
}
```
<br>

▶︎ 출력
```
John is male? true
Julie is a married women? true
```

---

# # 참조 사이트

- [위키백과](https://ko.wikipedia.org/wiki/인터프리터_패턴)
- [interpreter_설명블로그1](https://m.blog.naver.com/PostView.nhn?blogId=2feelus&logNo=220664898533&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [interpreter_설명블로그2](https://kunoo.tistory.com/entry/행위-패턴-Interpreter-pattern-인터프리터-패턴)


