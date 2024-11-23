# Furnace Process

### Furnace 공정의 정의

1. Furnace 공정은 크게 산화공정, 확산공정 및 LP-CVD 공정으로 나눔

2. 반도체 제조 공정에서 Metal 공정 이전의 산호공정, 열처리 공정 및 Film 중착 공정을 주로 담당

3. 실리콘 웨이퍼에 반도체 칩을 제조하기 위해 공정을 진행하는데 가장 초기 공정을 담당

### 공정의 이해

- 산화(Oxidation) 공정

    - 실리콘 웨이퍼의 표면을 산소 또는 수증기와 반응시켜 실리콘 산화막을 형성하는 공정

- 확산, RTP 공정

    - 물질이 매질을 통해 고농도에서 저농도로 움직이는 현상으로 반도체 열처리 제조 공정에 적용

- LP-CVD 공정

    - 반응 Gas를 반응로 내로 주입하여 650~ 850℃ 정도의 온도와 저압상태에서 여러 가지 종류의 막질을 실리콘 웨이퍼 표면에 중착하는 공정
 
### 실리콘 산화(Oxidation)의 용도

- 게이트 절연막 : 소스와 드레인 사이 전기적 신호를 연결 또는 차단시키는 역할

- 커페시터 유전막
    - 전하를 저장하는 소자 : 전하저장능력이 큰 물질들이 사용
    - 커페시터 구성 : 양극과 음극 및 그 사이 유전물질로 구성
    - 정전용량 (Capacitance) : 전화를 축적(저장)할 수 있는 능력

- 소자간 격리 : 소자와 소자 소이를 물리적/전기적으로 격리시키는 것
    - LOCOS (Local Oxidation of Silicon) 주요 공정
        - Silicon -> SiN 증착 -> PR 패터닝 -> SiN 식각 -> SiO2 Growth -> SiN Etching
  
    - LOCOS 공정의 특징
        - STI 공정보다 단순함
        - Field Oxidation 시 발생하는 Bird's Beak 발생 (Active Area 감소 -> 공정미세화 적용불가)
    
    - STI (Silicon Trench Isolation)
        - 공정이 LOCOS 공정보다 복잡
        - 0.25um 이하의 소자공정에 사용
        - Silicon -> SiN 증착 -> SiN 식각 -> Si Trench 식각 -> SiO2 Deposition -> SiO2 CMP -> SiN Etching

- 확산 및 이온주입 시 마스크

- 금속 층간 분리
    - 금속배선 층과 금속배선 층간 격리 (절연)
    - 적용되는 SiO2는 성장(Growth)방식이 아닌 증착(Deposition)방식으로 공정을 진행하여야 함  
    
    ※ 열산화 방법으로 불가능한 이유 : 열산화 법은 고온(800℃ 이상)에서 진행되므로 공정 시 금속배선 손상

- 오염방지 Passivation
    - 반도체 표면에 막을 증착하여 반도체 소자의 특성을 안정화 시킴
    - 기계적, 화학적 손상 뿐만 아니라 알카리 이온수 나 수분등의 침투방지

### 반도체 재료로 실리콘이 가장 많이 쓰이는 이유?

- 풍부한 자원 (전체 지각질량의 27.7% 차지)
- 안정한 결합 (공유결합) 구조로써 환경이나 인체에 무해
- 전기전도율 조절가능 (불순물 Doping)
- 고순도 실리콘 정제기술 개발로 단결정 성장 가능
- 산소와 쉽게 반응하여 산화막(SiO2) 형성 가능

### 열산화(Thermal Oxidation)의 종류

-정의
    - 고온의 전기로(Furnace)에서 산화성 분위기를 만들어 진행
    - 산화막의 두께, 산화막의 용도, 소자의 구조적인 관계 등을 고려하여 최적의 산화방법을 선택

- 건식 산화 (Dry Oxidation)
    - 가장 일반적으로 많이 사용되는 방법
    - 사용하는 Gas인 Dry O2의 수분농도를 1ppm이하로 정제한 것을 사용
    - 산화막 증착에 관여하는 산소는 분자가 이온화한 산소가 관여
    - 산화막 내에는 고농도의 산소 공격자 V²와 격자간 산소 O² 존재

### 열산화(Thermal Oxidation)의 방법

- 건식 산화 (Dry Oxidation)
    - Si(Solid) + O2(Gas) -> SiO2(Solid)
    - Silicon 산화 공정에서 가장 일반적으로 많이 사용되는 방법
    - 산화에 사용되는 Dry O2는 수분을 정제한 것을 사용
    - 고품질 막형성이 가능하나 산화속도가 West/Steam 산화에 비해 성장속도가 느리기 때문에 얇은 산화막에 적용
 
- 습식 산화 (Wet Oxidation)
    - 산화 분위기내로 수분성분이 포함되면 실리콘의 산화속도가 빨라짐
    - 두꺼운 산화막을 형성해야 하는 경우 800 ~ 1000℃에서 단시간에 산화 진행
    - 수증기는 별도의 외부 연소장치에서 수소를 연소시키는 Pyrogenic 방식으로 발생시키는 것이 일반적
    - H2/O2 Gas비를 0 ~ 2.0 사이로 변화시킴으로써 Dry 산화 및 Wet 산화, Steam 산화 등의 분위기를 만들 수 있음
    - SiO2 중의 H2-O는 SiO2의 결합을 약하게 하는 원인을 제공하고, 수소는 H2-O와 Si의 반응에 대해 촉매작용을 함
 
### 산화 성장에 영향을 주는 요소

- 공정시간이 길수록 산화막의 두께 증가

- 공정온도가 높을수록 성장속도 증가

#### 건식 VS 습식 성장속도

- 건식산화법도가 습식산화법의 성장속도가 5 ~ 10배 빠름 (H2O의 용해도가 O2의 용해도보다 큼)

#### 압력

- 압력이 높을수록 산화율이 증가
- 반응할 수 있는 oxygen의 농도를 증가시켜 O2 or H2O 용해도를 크게 함

#### 불순물 영향

- 반도체 공정에서 주로 사용하는 Dopants는 B과 P, As 인데 Silicon에 B나 P, As가 Doping 되었을 경우, Oxidation rate는 크게 달라짐

> Doped Wafer에서 Oxidation rate가 증가되는 이유
> Dopants에 의해 Silicon의 Bonding Energy가 감소하여 Reaction Rate Constant가 커지기 때문이며, Oxide의 두께가 두꺼워지면 Oxygen의 Diffustion이 좌우하기 때문에 Dopants이 영향은 크게 감소

#### 불순물 도핑농도

- 도핑농도의 증가에 따라 산화막의 두께도 증가 (강하게 도핑된 실리콘은 약하게 도핑된 물질보다 더 빠른 속도로 산화)

#### Halogen 첨가 (CI)

- Oxidation rate 증가 (CI이 oxygen의 diffusivity를 증진시킴)
- Oxide quality와 Oxide-Silicon interface가 개선됨
- 보통 HCI(3% ~ 7%)이 chlorine source로 사용되었다가 부식성 때문에 TCA(trichloroethan) or TCE(trichloroethylene) 등이 대체되어 넓게 사용되고 있음

