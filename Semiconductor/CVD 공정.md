# CVD 공정

## CVD 공정의 이해

### 증착 (CVD : Chemicla Vapor Deposition)

- 웨이퍼에 박막을 증착하여 전기적 특성을 갖도록 만드는 것

- 반도체의 증착 공정 = 반도체에 필요한 박막을 제조

#### 과정
1. 챔버에 Wafer 들어감
2. 챔버(방)에 가스와 열을 가함
3. 가스가 화학작용 웨이퍼 위에 막 형성
4. 막을 여러 겹으로 얇게 입힘

### 공정의 정의

1. 반응기(Chamber) 안에 화학반응 물질을 기화시켜 기체들을 주입하며, 화학 작용에 의해 생성된 생성물이 웨이퍼 표면에 증착시키는 방법

2. 반응 기체들의 확산 및 흡착, 화학반응에 의해 진행

### CVD 공정 개념

- Film 원료를 함유하고 있는 Gas 화합물의 반응을 이용하여 기판 위에 Film을 증착시키는 공정

    1. Film은 고체, 나머지 부산물은 Volatile(휘발) 해야 함
    2. Activation Energy(반응 에너자) 가 존재해야함

- 반응의 Mechanism

    1. 원료/반응 Gas가 웨이퍼 표면에 도달
    2. 웨이퍼 표면에서 원료/반응 Gas의 흡착
    3. 웨이퍼 표면에서 화학반응 및 유동, 핵 형성
    4. 웨어퍼 표면으로부터 반응생성물의 이탈
    5. 웨이퍼 표면으로부터 반응생성물의 확산
 
### 공정에 영향을 주는 요소

- Film 원료를 함유하고 있는 Gas 화합물의 반응을 이용하여 기판 위에 Film을 증착시키는 공정
- 결정 Factor


#### CVD Process의 개략도

- 세정(Cleaning) -> 증착(Deposition) -> Photo Mask(Lithography) -> 식각(Etching)

#### CVD 공정의 요구조건

1. 원하는 구조, 적은 이물질, 전기적 및 기계적 성질의 우수성을 가져야 함
2. 증착된 Film의 균일도
3. 좋은 Step Coverage
4. 증착된 부분에 빈 공간이 없어야함
5. 평평하게 된 박막이 필요

### 박막 구분

- Conductor (전도체) : Free Charge(전하)가 존재하여 전자의 이동이 활발

- Dielectric (부도체) : Charge가 분자에 묶여 전자의 이동이 어려움

- Semiconductor (반도체) : Conductor와 Insulator의 중간물질

### 박막형성 방법

- PVD (Physical Vapor Deposition) : 물리적인 힘에 의해 대상물질을 기판 위에 증착하는 방법

- CVD (Chemical Vapor Deposition) : 반응기체의 화학적인 반응에 의해 기판에 증착하는 방법

- ALD (Atomic Layer Deposition) : 윈자층 단위의 화학반응으로 증착하는 방법

- 전기도금 (Electro Plathing) : 전해질 용액 속에서 금속이온의 이동에 의해 박막이 증착되는 방법

### Growt VS CVD

|Growth|Deposition|
|--|--|
|O2 : 주입되는 가스|O2 : 주입되는 가스|
|Si : 기판(Si Wafer)|Si : 주입되는 가스(SiH4, ...)|
|Oxide(SiO2)가 실리콘 기판으로부터 위, 아래로 성장|Oxide(SiO2)가 실리콘 기판으로부터 증착, 위, 아래로 성장|
|고온 공정|저온 공정|
|고품질|높은 증착률|

## CVD 공정별 분류

### 공정방식에 따른 분류

<table>
    <tr>
        <th>Process</th>
        <th>Advantage</th>
        <th>Disadvantage</th>
        <th>Application</th>
    </tr>
    <tr>
        <td rowspan=3>APCVD</td>
        <td>간단한 장치</td>
        <td>Poor step coverage</td>
        <td>Low temp oxide (BPSG)</td>
    </tr>
    <tr>
        <td>빠른 속도</td>
        <td>Particle</td>
        <td>필림 평탄화 (Planarization)</td>
    </tr>
    <tr>
        <td>낮은 온도(500 ~ 550℃)</td>
        <td>낮은 효율</td>
    </tr>
    <tr>
        <td rowspan=3>LPCVD</td>
        <td>탁월한 순도</td>
        <td>High temperature (400 ~ 900℃)</td>
        <td>High temp oxide (SiO2)</td>
    </tr>
    <tr>
        <td>균일도 우수</td>
        <td>Low Deposition Rate</td>
        <td>Si Nitride (SiN)</td>
    </tr>
    <tr>
        <td>탁월한 Step Coverage</td>
        <td></td>
        <td>텅스텐 : CVD방식의 유일한 금속막</td>
    </tr>
    <tr>
        <td rowspan=3>PECVD</td>
        <td>낮은 온도(350 ~ 400℃)</td>
        <td>Chemical 오염</td>
        <td>IMD 절연막 (SiO2, SiON, SiN)</td>
    </tr>
    <tr>
        <td>빠른 속도</td>
        <td>Particle</td>
        <td>Passivation</td>
    </tr>
    <tr>
        <td></td>
        <td>Poor Step coverage</td>
        <td></td>
    </tr>
</table>

### CVD 박막이 갖추어야 할 특성

- 우수한 단차피복
- 높은 종횡비
- 박막의 우수한 균일도, 순도, 밀도
- 박막과 기판과의 점착력이 우수할 것
- 화학적 조성 조절이 우수할 것
- 필름 스트레스가 작을 것

### Film Stress (응력) 영향

#### 내부 응력

- 박막 증착 시 박막 자체에서 발생하는 응력
- 박막 증착 시 불순물의 존재
- 박막 증착 시 박막물이 기판과 충돌
- 박막 증착 시 팽창 및 수축

#### 외부 응력

- 박막과 기판 사이의 상호작용에 의해 발생
- 박막과 기판 사이의 열팽창계수차이에 따른 열응력
- 박막과 기판 간의 상호 확산이나 화학반응에 의해 발생

#### 인장 응력

- 박막이 성장할 때 수축성을 가지면 다시 원복되려는 응력발생
- 기판 표면은 상대적으로 압축
- 기판 나머지는 팽창 -> 기판은 전체적으로 팽창하면서 위로 휘어짐
- 박막의 변화 : 막 주름이나 갈라짐 발생

#### 압축 응력

- 박막이 성장할 때 팽창성을 가지면 다시 원복되려는 응력 발생
- 기판 표면은 상대적으로 팽창 

### 학습 출처

- 대림대학교 [부트캠프사업] 반도체입문 온라인 수업
