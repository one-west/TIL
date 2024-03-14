# Flex Guide

### Flex 설명
- Flex는 요소들의 배치 및 정렬을 편리하게 해주는 기능이다.
- 기본적으로 레이아웃은 수직 구성이며 <u>**위 아래**</u>로 스크롤 하여 사용한다.  
- 레이아웃 구성 시 가장 많이 사용하는 요소(Elements)들이 기본적으로 블록(Block) 개념으로 표시(Display)되며 이는 화면에 수직으로 쌓이기 때문에 수직 구성은 상대적으로 쉽게 구현이 가능한 반면,
수평 구성의 경우는 그렇지 않음.

- 문제 : 
    1. 수평 구조를 만드는 속성이 명확하지 않았고 그래서 많은 경우 `table`이나 `float` 혹은 `inline-block` 등의 도움을 받았다.  
    2. 위의 방법들도 다양한 문제(Clear, White space 등) 를 가지고 있다.

- 이러한 문제를 극복하고자 CSS3에서는 Flex(Flexible Box)라는 명확한 개념(속성들)으로 레이아웃을 쉽게 구성할 수 있다.😄

<br>

### Flex Box

- Flex는 2개의 개념으로 나뉜다.
    1. Container
    2. Items

![flexBox](https://css-tricks.com/wp-content/uploads/2018/11/00-basic-terminology.svg)

- Container는 Items를 감싸는 부모 요소이며, 각 Item을 정렬하기 위해선 Container가 필수입니다.

    > Container와 Items에 적용하는 속성이 구분되어 있다.  
    Container : `display`, `flex-flow`, `justify-content` 
    Items : `order`, `flex`, `align-self`

## Flex Container
Flex Container를 위한 속성

|속성|의미|
|---|---|
|display|Flex Container를 정의|
|flex-flow|`flex-direction`와 `flex-wrap`의 단축 속성|	
|flex-direction|Flex Items의 주 축(Main Axis)을 설정|
|align-content|교차 축(Cross Axis)의 정렬 방법을 설정(2줄 이상)|
|align-items|교차 축(Cross Axis)에서 Items의 정렬 방법을 설정(1줄)|
|gap|각 아이템 사이의 간격을 설정|

### display
- `display` 속성으로 Flex Container를 정의
- `display: block`, `display: inline-block`, `display: none` 같이 사용하는 경우가 많다.
- Flex 사용 시 `display: flex`, `display: inline-flex`로 정의

|값|의미|기본값|
|---|---|---|
|flex|Block 특성의 Flex Container를 정의||
|inline-flex|Inline 특성의 Flex Container를 정의||

- `flex`와 `inline-flex`의 차이
    - display: flex 는 수직
    - display: inline-flex 는 수평 

>여기서 말하는 수직과 수평 쌓임은 Items가 아니라 Container이다.  
두 값의 차이는 내부에 Items에는 영향을 주지 않는다.
---

### flex-flow
- 이 속성은 단축 속성으로 Flex Items의 주 축(main-axis)을 설정하고 Items의 여러 줄 묶음(줄 바꿈)도 설정

|값|의미|기본값|
|---|---|---|
|flex-direction|Items의 주 축(main-axis)을 설정|`row`|
|flex-wrap|Items의 여러 줄 묶음(줄 바꿈) 설정|`nowrap`|
---

### flex-direction
- Items의 주 축(main-axis)을 설정

|값|의미|기본값|
|---|---|---|
|row|Itmes를 수평축(왼쪽에서 오른쪽으로)으로 표시|`row`|
|row-reverse|Items를 `row`의 반대 축으로 표시||
|column|Items를 수직축(위에서 아래로)으로 표시||
|column-reverse|Items를 `column`의 반대 축으로 표시||
---

### 주 축(main-axis)과 교차 축(cross-axis)
- Items가 수평으로 표시되면, 이때는 주 축이 수평이며 교차 축은 수직이 된다.
- 반대로 수직으로 표시되면, 주 축은 수직이며 교차 축은 수평이 된다.
- 즉, 방향(수평, 수직)에 따라 주 축과 교차 축이 달라진다.
---

### 시작점(flex-start)과 끝점(flex-end)
- 주 축이나 교차 축의 시작하는 지점과 끝나는 지점을 지칭한다.
- 방향에 따라 시작점과 끝점이 달라진다.
---

### flex-wrap
- Items의 여러 줄 묶음(줄 바꿈)을 설정한다.

|값|의미|기본값|
|---|---|---|
|nowrap|모든 Itmes를 여러 줄로 묶지 않음(한 줄에 표시)|`nowrap`|
|wrap|Items를 여러 줄로 묶음||
|wrap-reverse|Items를 `wrap`의 역 방향으로 여러 줄로 묶음||
---

### justify-content
- 주 축(main-axis)의 정렬 방법을 설정

|값|의미|기본값|
|---|---|---|
|flex-start(start)|Items를 시작점으로 정렬|`flex-start`|
|flex-end(end)|Items를 끝점으로 정렬||	
|center|Items를 가운데 정렬||
|space-around|각 Item 좌우 여백을 고르게 정렬||
|space-between|첫 Item은 시작점에, 끝 Item은 끝점에 정렬되고 나머지 여백으로 고르게 정렬||
|space-evenly|모든 여백을 고르게 정렬||
---

### align-content
- 교차 축(cross-axis)의 정렬 방법을 설정
- 주의할 점은 flex-wrap 속성을 통해 Items가 여러 줄(2줄 이상)이고 여백이 있을 경우만 사용할 수 있다.

|값|의미|기본값|
|---|---|---|
|stretch|Container의 교차 축을 채우기 위해 Items를 늘림|stretch|
|flex-start(start)|Items를 시작점으로 정렬||
|flex-end(end)|Items를 끝점으로 정렬||
|center|Items를 가운데 정렬||
|space-around|각 Item 좌우 여백을 고르게 정렬||
|space-between|첫 Item은 시작점에, 끝 Item은 끝점에 정렬되고 나머지 여백으로 고르게 정렬||
|space-evenly|모든 여백을 고르게 정렬||
---

### align-items
- 교차 축(cross-axis)에서 Items의 정렬 방법을 설정
- Items가 한 줄일 경우 많이 사용한다
- 주의할 점은 Items가 flex-wrap을 통해 여러 줄(2줄 이상)일 경우에는 align-content 속성이 우선된다.
- 따라서 align-items를 사용하려면 align-content 속성을 기본값(stretch)으로 설정해야 합니다.

|값|의미|기본값|
|---|---|---|
|stretch|Container의 교차 축을 채우기 위해 Items를 늘림|stretch|
|flex-start(start)|Items를 각 줄의 시작점으로 정렬||	
|flex-end(end)|Items를 각 줄의 끝점으로 정렬||
|center|Items를 각 줄의 가운데 정렬||	
|baseline|Items를 문자 기준선에 정렬||	
---

### gap
- 각 아이템 사이의 간격을 설정합니다.

|값|의미|기본값|
|---|---|---|
|단위|px, em, cm 등 단위로 지정|0|
---

<br>

## Flex Items
|속성|의미|
|---|---|
|order|Flex Item의 순서를 설정|
|flex|flex-grow, flex-shrink, flex-basis의 단축 속성|
|flex-grow|Flex Item의 증가 너비 비율을 설정|
|flex-shrink|Flex Item의 감소 너비 비율을 설정|
|flex-basis|Flex Item의 (공간 배분 전) 기본 너비 설정|
|align-self|교차 축(cross-axis)에서 Item의 정렬 방법을 설정|

---
### order
- Item의 순서를 설정한다.
- Item에 숫자를 지정하고 숫자가 클수록 순서가 밀립니다.
- 음수 허용

|값|의미|기본값|
|---|---|---|
숫자|Item의 순서를 설정|0|  
---

### flex
- Item의 너비(증가, 감소, 기본)를 설정하는 단축 속성

|값|의미|기본값|
|---|---|---|
|flex-grow|Item의 증가 너비 비율을 설정|0|
|flex-shrink|Item의 감소 너비 비율을 설정|1|
|flex-basis|Item의 (공간 배분 전) 기본 너비 설정|auto|

>`flex-grow`를 제외한 개별 속성은 생략할 수 있다.  
만약 `flex: 1`로 작성하면 `flex-grow: 1`과 같다  
즉 `flex: 1` 혹은 `flex: 1 1`은 `flex: 1 1 auto`와 같다고 볼 수 있습니다만 그렇지 않다.
`flex-basis`의 기본값은 auto이지만 단축 속성인 flex에서 그 값을 생략할 경우 0이 적용된다.
---

### flex-grow
- Item의 증가 너비 비율을 설정합니다.
- 숫자가 크면 더 많은 너비를 가집니다.
- Item이 가변 너비가 아니거나, 값이 0일 경우 효과가 없습니다.

|값|의미|기본값|
|---|---|---|
|숫자|Item의 증가 너비 비율을 설정|0|
---

### flex-shrink
- Item이 감소하는 너비의 비율을 설정합니다.
- 숫자가 크면 더 많은 너비가 감소합니다.
- Item이 가변 너비가 아니거나, 값이 0일 경우 효과가 없습니다.

|값|의미|기본값|
|---|---|---|
|숫자|Item의 증가 너비 비율을 설정|0|
---

### flex-basis
- Item의 (공간 배분 전) 기본 너비를 설정합니다.
- 값이 auto일 경우 width, height 등의 속성으로 Item의 너비를 설정할 수 있다.
- 단위 값이 주어질 경우 설정할 수 없습니다.

|값|의미|기본값|
|---|---|---|
|auto|가변 Item과 같은 너비|auto|
|단위|px, em, cm 등 단위로 지정||
---

### align-self
- 교차 축(cross-axis)에서 개별 Item의 정렬 방법을 설정
- `align-items`는 Container 내 모든 Items의 정렬 방법을 설정
- 필요에 의해 일부 Item만 정렬 방법을 변경하려고 할 경우 `align-self`를 사용할 수 있습니다.
- 이 속성은 align-items 속성보다 우선합니다.

|값|의미|기본값|
|---|---|---|
|auto|Container의 align-items 속성을 상속받음|auto|
|stretch|Container의 교차 축을 채우기 위해 Item을 늘림||
|flex-start(start)|Item을 각 줄의 시작점으로 정렬||
|flex-end(end)|Item을 각 줄의 끝점으로 정렬||
|center|Item을 가운데 정렬||
|baseline|Item을 문자 기준선에 정렬||
---

## 자료출처
- https://css-tricks.com/snippets/css/a-guide-to-flexbox/
- https://www.heropy.dev/p/Ha29GI