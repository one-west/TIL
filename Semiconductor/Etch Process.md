# Etch Process

## ETCH (식각)

### 웨이퍼 위에 그린 회로를 원하는 부분까지 깎아내는 것

- 반도체의 식간 공정 = 판화처럼 벗겨내는 작업 (음각 / 양각)

- 특수 물질로 벗겨냄

- 건식 식각: 가스로 제거하는 건삭

- 습식 식각: 화확액으로 제거하는 습식

- 회로를 새기기 위함

### 공정의 정의

- 반도체 제품에는 여러 종류의 박막재료가 사용되는데 박막 위에 Photo Resist을 Coating 하고 Mask Patterning을 하는데 패턴이 되어있지 않은 부분을 화학적 또는 물리적 반응을 이용하여 제거하는 공정

- Mask Pattern으로 사용하였던 Photo Resist를 제거하는 공정을 PR Strip (Ashing) 공정

- Mask Pattern 없이 Film을 중착한 상태에서 전면 Etch 및 제거하는 공정을 Etch Back 또는 Blank Etch

![Etch공정.jpg](https://images.velog.io/images/tataki26/post/f818e757-e7d8-4c5d-bafa-6b7e6877c6da/%EC%8B%9D%EA%B0%81%EC%A0%84%ED%9B%84.jpg)

- Conventional Dry Etching

    - Si Sub. -> Metal 증착 -> PR Coating -> DEV -> Dry Etching -> PR Strip
 
### Etch Process의 순서

- 박막 증착

- Photo 고정

- Etch 공정

- Ashing 공정

- Cleaning 공정

- 측정/검사

### Etch 공정과 주변 공정 관게

![Etch 공정과 주변 공정 관게](https://github.com/user-attachments/assets/89417071-c740-4022-9e52-4b1c5aa9a3b0)

1. Line 및 Hold 등 Pattern 형성

2. Etch 및 Implant 후 Ashing 및 Cleaning 진행

3. 박막 증착 후 Mask Pattern 공정을 하지 않고 Etch 하는 공정

4. Mask Pattern 공정 후 Implant 진행

5. 박막 증착 및 CMP 공정 후 Mask Pattern 공정 진행

6. 박막 증착 후 CMP 공정 및 CMP 공정 후 박막 증착 진행

### 공정의 분류

1. Etch 형태에 따른 분류

    - Isotropic Etch (등방성 식각)

        - 모든 방향(아래쪽과 좌우 방향)으로 식각률이 동일한 Etch
     
        - Wet Etch 및 Dry Etch 모두 가능하나 주로 Wet Etch에서 나타냄
     
        - 특징 : 아래쪽으로 Etch 되는 량에 비례해서 Side Etch 됨
     
        - 적용 : 단차가 심한 공정에서 Stringer 제거 및 Metal contact hole 등에서 Step coverage 향상을 위하여 사용
 
    - Anisotropic Etch (이방성 식각)

        - 주로 Gas를 이용한 Dry Etch(건식각) 경우 필요한 부분만 선택적으로 Etch 함
     
        - Etch 특성이 우수하다.
     
        - 특징 : 주로 아래쪽으로 한 방향으로 Etch 함
     
        - 적용 : 미세 Pattern 가공에 우수 (Etch 후의 Pattern이 정교하고 직진성이 우수)
     
    - 기타 Etch 형태
  
        - Under cut Etch : Top Under cut과 Bottom Under cut 구분
     
        - Side Etch : 등방적이라기 보다 횡방향으로 빠르게 진행되는 경우
     
        - 특징 : Dry Etch의 경우 공정 Parameter 조절로 Profile 조절이 가능
     
        - 적용 : 공정 특성에 따라 적용할 수 있음
     
2. Etch 방법에 따른 분류

    - Wet Etch (습식각)

        - Etch 하고자 하는 박막의 성질에 따라 화학약품(Chemical Etchant)을 선택하여 사용
     
        - 박막 재료, 절연물(SiO2, Si3N4 등), 금속 등 용해성을 고려하여 선택

        - Side Etch가 많이 되므로 고집적화 및 미세 Pattern 형성 공정에는 부적합

        - 특징 : Isotropic Etch (등방성 식각)
     
        - 적용 : Side Etch가 많이 되므로 특정공정 또는 세정공정 전면 Etch의 경우 사용

        - 반응 과정 : 확산 -> 흡착 -> 반응, 생성 -> 탈착 -> 확산

        - Wet chemical etching에 영향을 미치는 3단계

            - 표면으로부터 확산반응에 의해 반응물로 변환
            - 표면에서 화학 반응 발생
            - 표면으로부터 생성된 반응물은 확산반응으로 제거

        - 식각 장치에 의한 분류 : Single Type, Batch Type

        - 식각 박막에 의한 분류 : Nitride Film Etch, Poly-Silicon Film Etch, Oxide Film Etch, Metal Film Etch, Photo Resist Strip, Wafer Cleaning
    
    - Dry Etch (건식각)
    
        - 반응성 Gas을 진공 Chamber에 주입시킨 후 Power을 인가하여 Plasma을 형성한 상태에서 반응성 으온의 화학적, 물리적 반응을 이용하여 막질을 Etch하는 방법

        - Etch 후나 Implant 후 PR을 제거하는 Dry Ashing
     
        - 특징 : Anisotropic Etch(비등방성/이방성 식각), Vertical Etch, Etch Back 등
     
        - 적용 : Side Etch가 없어야 하는 Line Define 공정 및 Deep Contact 공정 등

    |Wet Etch (습식각)|Dry Etch (건식각)|
    |--|--|
    |화학약품|반응성 Gas|
    |동방성 식각|이방성 식각|
    |미세회로 패턴 조절이 어렵다|미세회로 패턴 가공 가능|
    |하부 막질에 대한 Damage 적다|하부 막질에 대한 Damage 발생|
    |Polymer에 의한 오염발생이 없다|Polymer에 의한 오염발생|
    |Etching 종료시간 조절이 어렵다|Etch 종료시간 조절이 용이하다|
    |현재 대부분 Cleaning 공정에 적용|현재 대부분 공정에 적용|
    
    |식각 박막 구분|분자식|사용 Gas
    |--|--|--|
    |Nitride Film Etch|Si3N4 (질화막)|SF6, CF4, CI2
    |Poly-Silicon Film Etch|Si (다결정 실리콘 막)|CI2, SF6, CF4, BBr, HCI 등
    |Oxide Film Etch|SiO2 (산화막)|CF4, CHF3, C2F6, C4F8 등
    |Metal Film Etch|AI, MoSi, TiW, TiN, W|CI2, BCI3, SF6, CF4, CHF3 등
    |PR Strip|C|O2, O3

3. Etch 반응에 따른 분류

    - Chemical Etch (화학적 식각)

        - 주로 라디칼에 의한 반응을 막질과의 화학반응을 일으켜 식각
     
        - 하부 막질의 물리적 손상이 거의 없고 선택비가 높음
     
        - 화학적 반응성이 높기 때문에 등방성 식각의 특성을 갖게됨
    
    - Physical Etch (물리적 식각)
  
        - 주로 불활성화 된 이온이 외부에서 인가된 에너지(PF Power)를 가지고 웨이퍼 표면에 물맂거 충돌을 일으켜 막질의 결정 구조를 깨뜨리며 식각
     
        - 식각 할 막질에 대한 물리적 손상이 심하고 선택비가 낮음
     
        - 전계방향에 따라 이온들이 직진성을 갖기 때문에 강한 이방성(Vertical) 식각 특성을 갖게됨

    - Reactive Ion Etch (RIE Type) (물리적, 화학적 반응 식각)
  
        - Plasma를 이용하여 Etch하는 방식 중 하나
     
        - 주로 이온에 의한 식각 방법으로 물리적 반응과 화학적 반응의 효과를 동시에 이용
     
        - 이온의 물리적 충돌로 막질의 결정 구조를 깨뜨리면서 Radical 성분의 화학적 반응을 이용하여 식각하므로 이방성 식각이 가능

### Plasma의 이해

- **개념**

    - 전기적으로 하전된 입자(이온과 전자)와 중서입자(라디칼)들이 모여 있는 상태로서 전체적인 중성의 성질을 지닌 물질

    - (-)로 하전된 입자(전자)의 밀도와 (+)로 하전된 입자(이온)의 밀도 및 (*) 라디칼의 중성입자가 생성된 상태를 유지하여 거시적으로 중성상태의 물질로 제4의 물질 상태
 
    - 대부분 Dry Etch 라고 하면 Plasma Etch를 의미

- **발생 과정**

    - 진공 Chamber 내에 반응성 Gas를 주입하고 전극판에 교류전압을 인가
 
    - 전극판 사이에서 전자가 발생되고 이 전자가 반응성 Gas와 충돌하여 여기상태, 분리, 이온화가 발생
 
    - 전자는 중성입자들보다 보통 2000배 이상 빠르게 운동하기 때문에 크고 작은 충돌 반응으로 Gas 중성입자들을 이온화 시킴

    - 이온화 과정에서 생성된 Gas 이온들과 불완전한 분자구조를 갖는 라디칼들은 전기장내에서 받은 에너지에 의해 웨이퍼면에 반응성을 가지고 충돌
 
    - 생성된 이온들과 라디칼이 Etch 하고자 하는 박막과 반응이 일어나 Etch 하게 됨

### Loading Effect

- 패턴밀도(Density)차이에 따라 식각속도가 달라지는 현상

- 동일한 양의 반응물을 공급하면(플라즈마 밀도를 같게 할 경우), 식각면적이 증가할수록 식각률(E/R)은 감소하게 된다.

> ※ Etching 되는 부분이 많이 노출되어 있는 영역
> Etching Reaction에 의한 에칭가스의 소모 증가 -> 에칭가스 농도감소 -> 에층 속도 감소

- 해결방법

    - High Pressure
    - High Source Power
    - Low Bias Power(Slope)
    - Large Gas Flow
    - Etching되는 것과 똑같은 물질로 전극 제작
    - lon bombardment(이온폭탄)에 의한 에칭속도가 제어

### 학습 출처
- 대림대학교 [부트캠프사업] 반도체입문 온라인 수업
