---
title:  "github.io 블로그 시작하기"
excerpt: "GitHub Blog 테스트용 입니다."
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
# toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
categories:
  - Blog
tags:
  - Blog
last_modified_at: 2020-07-06T00:00:00-02:00
---
(개인약속)
categories : 대분류 / tags : 소분류

YFM에서 정의한 제목을 이중 괄호 구문으로 본문에 추가할 수 있다.
이 글의 제목은 {{ page.title }}이고
마지막으로 수정된 시간은 {{ page.last_modified_at }}이다.

아래는 블로그 테스트입니다.

---
Python Tag
```python
input()
```


- 리스트
- 형식

```
- 리스트
- 형식
```

***

* 음식
    * 밀가루
        * 라면

```
* 음식
    * 밀가루
        * 라면
```

+ 음식
    + 밀가루
        + 라면

```
+ 음식
    + 밀가루
        + 라면
```

- 음식
    - 밀가루
        - 라면

```
- 음식
    - 밀가루
        - 라면
```

***

1. 다른
2. 형식

```
1. 다른
2. 형식
```

***

- 강조하는법

*강*
_조_
**하**
__는__
~~법~~

```
*강*
_조_
**하**
__는__
~~법~~
```

***

# h1
```
# h1
```
## h2
```
## h2
```
### h3
```
### h3
```
#### h4
```
#### h4
```
##### h5
```
##### h5
```
###### h6
```
###### h6
```

***

- 인용문구
> 인용문구 1.
```
> 인용문구 1.
```

>> 인용문구 2.
```
>> 인용문구 2.
```

>>> 인용문구 3.
```
>>> 인용문구 3.
```

***

- 코드인용

function test(){
    console.log("hello");
}

```
function test(){
    console.log("hello");
}
```

***

- 수평선만들기

* * *
***
*****
- - -
---------------------------------------

```
* * *
***
*****
- - -
---------------------------------------
```

***

- 링크

1. 링크 표시법

```
- 링크표시법 : [Title](link)
[Google 페이지 링크](https://google.com)
```

[Google 페이지 링크](https://google.com)

2. 주소 직접 표시법

```
<https://google.com>
```

<https://google.com>

***

- 이미지 삽입

![설명](https://fifabell.github.io/assets/images/jji_tiger_logo.jpg "썸네일"){: .align-center}

```
![설명](https://fifabell.github.io/assets/images/jji_tiger_logo.jpg "썸네일"){: .align-center}
```

***

- 표 만들기

| 항목 | 가격 | 개수 |
|:----:|:----:|:----|
| 라면 | 800원 | 10개 |
| 과자 | 900원 | 20개 |

```
| 항목 | 가격 | 개수 |
|:----:|:---:|:----|
| 라면 | 800원 | 10개 |
| 과자 | 900원 | 20개 |
```

***

- YFM 설정

1. toc 설정

```
toc: true
toc_sticky: true # 화면 넘어갈때 고정 여부
toc_label: "페이지 주요 목차" 를 직접 입력할 수 있다.
```

2. 타이틀

```
title:  "github.io 블로그 시작하기"
```

3. 내용(미리보기 설명)

```
excerpt: "GitHub Blog 테스트용 입니다."
```

4. 카테고리 분류

```
categories:
  - Blog
```

5. 태그 추가

```
tags:
  - Blog
```

6. 마지막 수정일 저장

```
last_modified_at: 2020-07-06T00:00:00-02:00
```