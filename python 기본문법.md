# Python의 기본문법
### 자료형
- 숫자
```py
+, -, /, * 다른 언어와 같음

print(5/3) <- 나누기
print(5//3) <- 몫
print(5%3) <- 나머지
print(5**2) <- 거듭제곱

int 클래스를 지원함
```
- 문자열
```py
"" 큰따옴표로 표시
str 클래스 존재를 지원함.
```
- boolean
```py
print( 2 > 1 )      # True
print( 1 > 2 )      # False
print( True )       # True
print( False )      # False
print( not True )   # False
print( not False )  # True

bool 클래스를 지원함.
```
---
### 변수
- Java와 달리 변수 선언 시 자료형을 지정하지 않아도 됨
```py 
animal = "고양이"
age = 3
name = "한동이"
hobby = "냥냥펀치"
```
---
### 주석
```py
주석을 표현하는 방법
# '해당 내용은 주석입니다.'

'''
해당 내용도 주석입니다.
범위 주석입니다.
'''

단축키 : ctrl + /
```
---
### 각종 연산자
```py
and 연산자 #Java에서는 &&
or 연산자 #Java에서는 ||
not 연산자 #Java에서는 !
```
---
### 슬라이싱
- 연속적인 개체 (ex> 리스트, 튜플, 문자열) 의 범위를 선택하여 객체들을 가져오는 방법을 말함 

- 슬라이싱을 하면 새로운 객체를 생성함

```py
a라는 연속적인 객체들의 자료구조가 있다고 가정을 했을 때 기본 형태는 아래와 같음.

a[start : end : step]

# start : 슬라이싱을 시작할 시작위치
# end: 슬라이싱을 끝낼 위치
# step: 몇개씩 끊어서 가져올지와 방향을 정함
```
- 각각 start, end, step 모두 양수와 음수를 가질 수 있습니다.
- 양수: 연속적인 객체들의 제일 앞에서부터 0을 시작으로 번호를 뒤쪽으로 증가하면서 매깁니다.
- 음수: 연속적인 객체들의 제일 뒤에서부터 -1을 시작으로 번호를 앞쪽으로 감소하면서 매깁니다.
---
### 문자열 포맷팅
- 문자열에서 특정문자만 변경하고 싶을 때 사용함
```py
# # 방법 1
print("내 나이는 %d살입니다." %25)
print("나는 %s을 좋아해요." %"파이썬")
print("ABCD는 %c로 시작해요." %"A")
print("내가 좋아하는 색은 %s색과 %s색입니다." % ("파란", "빨간"))

# 방법 2
print("내 나이는 %d살입니다." .format(20))
print("내가 좋아하는 색은 {}색과 {}색입니다." .format("파란", "빨간"))
print("내가 좋아하는 색은 {1}색과 {0}색입니다." .format("파란", "빨간"))
print("내가 좋아하는 색은 {0}색과 {1}색입니다." .format("파란", "빨간"))

# 방법 3
print("나는 {age}살이며, {color}색을 좋아해요." .format(age=25, color="빨간"))

# 방법 4 (v3.6 ~ 이상)
age = 20
color = "빨간"
print(f"내 나이는 {age}살이며, 좋아하는 색은 {color}색입니다.")
```
---

### 참고자료  
[https://wikidocs.net/book/1](https://wikidocs.net/book/1)  
[https://www.youtube.com/@nadocoding](https://www.youtube.com/@nadocoding)