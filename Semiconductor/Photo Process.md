# Photo Procss

### 사전적 의미의 Photo 공정

- Photo는 사전적 의미로 원래는 Photo Lithography 에서 따온 것으로 반도체 제조공정의 가장 핵심 공정이라고 할 수 있다.

- Lithography는 독일어 "Lithographie" 에서 유래되었는데, 그리스어인 "Lithos (돌)"과 "Graphein (쓰다)"가 합성된 것이다.

### Photo Process 란?

- 회로의 Pattern (패턴)을 Wafer (웨이퍼)에 옮기는 공정으로 얼마나 미세한 패턴을 만드느냐 즉, 집적도를 결정하는데 가장 중요한 공정이다.

- 웨이퍼 위에 복잡한 패턴을 반복적으로 복사해 내는 공정을 사진(Photo) 공정이라 한다.

### Photo 공정의 주요 3단계

- Diffusion : Film 증착

- PHOTO : 감광제 도포 -> 노광 -> 현상

- ETCH : 식각

### Photo 공정의 흐름도

![Photo 공정의 단계](https://images.velog.io/images/katie505/post/c0f8fb2b-eb02-4b0b-992c-2c7534dd3ca2/image.png)

- HMDS(Hexa Methylene DiSiloxane)
    
    - Adhesion(접착) : Wafer 위에 PR이 잘 붙도록 "화학처리"를 해주는 공정(풀 역할)

    - Track 장비에서 가압시킨 N2 가스가 HMDS를 이동시키는 기상도포방식
 
    - Wafer Silicon과 화학반응을 일으켜 접착성을 향상시키는 역할을 한다.

- 도포(P.R 코팅)

    - PR(Photo Resist)을 Wafer의 전면에 고르게 덮는 공정으로 Track 장비에서 이루어지고, Spin Spray 방식이다

- 노광(Exposure)

    - 도포 단계에서 코팅된 PR 위에 Pattern 형태의 빛을 쪼여서 감광 물질을 반응시키는 공정으로 Stepper 혹은 Scanner 장비에서 이루어짐 

- 현상(Develop)

    - 노광 단계에서 반응한 감광 물질 중 필요치 않는 부분을 제거하는 공정 Track 장비에서 이루어짐

- 검사(Inspection): 형성된 패턴이 설계와 일치하는지 확인하는 단계

    - Develop 공정까지 마친 Wafer 위에 패턴이 올바르게 형성되었는지 검사하는 공정
 
    - Visual, Overlay, CD 측정의 단계로 구분. 측정에 따라 장비가 달라진다.

> ※ 차례대로 진행한 다음 검사 단계에서 문제 발생 시 **Rework** -> PR Coating 단계로 재작업을 실시한다

### Photo 공정 재료

- Stepper : 마스크에 담긴 패턴을 웨이퍼 위에 투영하는 장비

- Track : 포토레지스트 도포 및 현상 처리를 담당하는 장비

- Scanner : Stepper와 유사하게 마스크 패턴을 웨이퍼에 투영하는 장비

- Mask(또는 Photomask) : 반도체 칩 제조 시 설계된 복잡한 패턴을 웨이퍼 위에 복사할 때 사용하는 장비

### Photo 공정 장비

- Track : 웨이퍼에 감광물질(Photoresist)을 도포하고 현상(Develop)을 수행하는 장비

- Scanner : 마스크에 있는 패턴을 웨이퍼에 투영하는 장비. 고해상도 패턴 형성을 위해 사용

- Stepper : 마스크 패턴을 웨이퍼에 반복적으로 투영하며, 주로 특정 영역만 노광하는 데 사용

### Photo Resist

- 광(Photo) 반응에 의하여 비 노광 영역고 노광 영역 간의 용해도 차이가 발생하여, 현상액에 의하여 패턴을 형성하는 화학 재료

- 마스크에서 전사된 원판 형상을 패턴호 하는 역할과 후속 식각 공정에서 막(Resistance) 역할 => Photo + Resist

- Wafer에 얇게 Coating 한 후 선택적으로 빛을 노출시킨 후 Development 과정을 거치면 노출, 또는 노출되지 않았던 Resist가 제거되면서 패턴이 나타나게 됨

    - Positive Resist : 빛에 노출되었던 Resist가 Development 과정에서 제거되는 형태
 
    - Negative Resist : 빛에 노출되지 않았던 부분이 제거되는 형태

- 카본(C)이 포함된 유기고분자로써 자외선에 노출될 때 그 고분자 사슬(PalymerChain)의 화학적 구조가 변하는 물질

- 흡수되는 파장대역에 따라 (g-Line, i-Line, Deep UV, X-ray, Elctron) 각각의 특성에 맞는 Resist가 사용, 개발되고 있음

- g- : 436nm, i- : 365nm, KrF : 248nm, ArF : 193nm
  
- PR Coating

    - Base Resin (Polymer, 고분자)
 
    - Organic Solvent (유기용제)
 
    - Photoactive compound : 빛과 반응하여 Polymer 결합을 끊어주는 이온 물질을 생성
 
    - Resin : Polymer 합성물질로 Chain 형태로 결합되어 있다
 
    - Solvent : PAC와 Resin의 점도를 결정

- E.B.R (Edge Bead Removal)

    - Wafer 가장자리에 존재하는 PR을 Thinner 등을 이용하여 제거하는 공정으로써 Coating과 동시에 진행되는 것이 일반적
 
    - Edge의 PR은 Defect를 일으킬 수 있다
 
- Soft Bake

    - Bake는 PR이 Coating된 Wafer를 Track장비의 Oven에서 일정한 온도로 구워내는 공정
 
    - 비교적 낮은 온도에서 Bake하여 Soft Bake라고 하며, PR의 점성을 향상시켜 Wafer 표면과의 결합력을 증가시키고, 구조를 견고하게 한다.
 
- Exposure

    - Exposure 공정은 빛과 그림자를 이용해 Pattern을 복사하는 공정
 
    - Pre Align : 진행할 공정에 맞는 Step의 마스크를 마스크 홀더(Holder)에 끼워 장비내의 마스크 위치에 정확하게 넣는 것
 
### Lithography

- DUV 시스템 적용에 따른 Wavelength Scaling

- Imaging System 발전에 따른 NA scaling

- Low k1 scaling

### 파장이 점점 작아지는 이유?

- ※ 더욱 작고 미세한 패턴을 분해해내기 위해서는 그에 걸맞는 짧은 파장을 이용해야 함

### 학습 출처

- 대림대학교 [부트캠프사업] 반도체입문 온라인 수업
