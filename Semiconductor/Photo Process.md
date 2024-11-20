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
 
    - 웨이퍼와 마스크 간에 Align 할 수 있도록 만들어진 Key를 매뉴얼(Manual, 수동) 또는 Auto(자동)로 실시한다.
 
    - 25~35 Layer 정도의 Photo Lithography(Mask공정) 공정을 진행해야 하며, 레티클 간 겹침이 발생하기 때문에 Align 과정은 무엇보다 중요한다.

    - Contact : Mask와 Wafer가 직접 접촉한 상태에서 1:1로 노광하는 방식으로 단가가 싸지만 접촉으로 인해 PR손상, 불순물 발생, Mask오염 등의 단점이 있다.
 
    - Proximity : Mask와 wafer 사이에 일정 간격을 유지함으로써 Contact방식의 단점을 극복했지만 빛의 회절, 발산으로 인한 원치 않는 Pottern 변화를 일으키게 된다.

    - Projection :  Contact 및 Proximity방식의 문제점을 해결 보완한 방식, Lens를 사용함으로써 Mask대 Pattern의 비율 조정이 가능하다.

- 현상(Develop)

    - 노광이 끝난 웨이퍼 위에 현상용액을 분사하여 노광 시 UV Light와 반응한 감광 물질 중 필요치 않는 부분을 제거하는 공정. Track 장비에서 이루어짐

    - 현상색은 온다에 민감하여 현상속도에 영향을 미치므로 엄격한 온도관리가 필요

    - 현상(Develop) 공정의 정의 노광 후에 마스크에 있던 패턴을 감광막으로 옮겨지게 되면, 웨이퍼 상에는 마스크 패턴에 따라 빛을 받은 부분과 받지 않은 부분이 생기게 된다.
  
    - 이것을 현상액을 이용하여 선택적으로 제거하여 패턴을 형성하는 공정

    - 현상액으로 1 ~ 2분 (상온~150℃) 현상(Development)하면 감광액의 특성에 따라 양성이면 노광된 부분이, 음성이면 노광되지 않은 부분이 제거됨으로써 비로서 감광원판의 패턴이 최종적으로 웨이퍼 위에 재현되는 것이다.

    - 현상 형성 종류
 
        - 현상공정은 웨이퍼를 회전시키면서 현상액을 분사하는 Spin Develop 방식이 보편적이며, 현상용액에 다량의 웨이퍼를 담금으로 현상하는 침전식(Bath Type)이 있다.
     
        - 회전식(Spin Spray) 방식의 분사 Nozzle에 따라 다음과 같이 분류한다.
     
        - E2 Nozzle 방식 : 넓은 면적을 한꺼번에 뿌려 균일한 Coating이 가능한 방식이다.
     
        - Stream Nozzle 방식 : 적당량을 분사하여 현상액의 소비를 줄이고 회전 속도로 Coating 능력을 조절한다.
     
        - H Nozzle 방식 : E2 노즐과 Stream 노즐의 장점을 융합하여 만든 방식이다.

    - P.E.B(Post Exposure Bake)
 
        - 노광을 진행하면서 PR은 빛의 간섭으로 단면에 파장형태의 굴곡을 남기게 된다.
     
        - 이를 없애기 위해 Hot Plate에 웨이퍼를 100~120℃의 일정한 온도를 가열해 줌으로써 단면에 생긴 굴곡을 완만하게 해주는 역할을 한다.
     
        - Standing Wave가 존재할 경우 DI CD의 불균일과 다음 공정인 Etch 곶정에 영향을 주어 Pattern 불량이 나타난다.
    
    - 현상 형성 종류

        - Puddle 방식 : 웨이퍼 위에 현상액을 분사하거나 흘린 다음 웨이퍼를 정지시켜 웨이퍼 위의 현상액이 현상하게끔 하는 방식
     
        - Spray 방식 : 현상액을 계속 분사 시키면서 현상하는 방법으로 연속공정에는 좋지만 대구경 웨이퍼에서는 선폭(CD)의 균일성이 나쁘면 현상액의 소모도 많다.
     
        - Dipping 방식 : 가장 효율적인 방식이나 연속 공정이 불가능하고 현상액의 소모가 만하 소규모 제조나 연구 개발시에 사용된다.

    - 현상에서의 중요사항

        - 현상액 량, 분사 시 Spray 압력, 온도 등이 Spec In/Out 되는지 확인
        
        - 현상액 교환 시 Develop Rate 확인

- Hard Bake

    - 현상 후에 웨이퍼 위에 남아 있는 Solvent나 수분을 제거
 
    - 패턴을 경화시켜 안정되도록 하는 역할
 
    - PR이 웨이퍼로부터 들떠 밀착되지 않으므로 적당한 온도의 열을 가해 PR을 굳혀줌으로써 물리적으로 견고하게 하여 식각 및 Implant 공정을 원할하게 함.
 
    - 하드 베이크는 Hot Plate에서 진행하고 온도는 120~180℃ 정도 온도가 높거나 시간이 길면 감광막이 오므라드는 현상(Contraction = Puddling) 및 식각, Implant 공정 후 감광막 제거가 어려운 결점이 있다.

- 검사(Inspection): 형성된 패턴이 설계와 일치하는지 확인하는 단계

    - Develop 공정까지 마친 Wafer 위에 패턴이 올바르게 형성되었는지 검사하는 공정
 
    - Visual, Overlay, CD 측정의 단계로 구분. 측정에 따라 장비가 달라진다.

    - 검사 방법
 
        1. 육안으로 웨이퍼의 Coating 불량 등을 검사하는 Visual Inspection
        2. 패턴이 이전 Layer와 정확히 찍혔는가를 검사하는 Overlay 측정
        3. 패턴의 크기 및 형성된 상태를 검사하는 CD 측정
        4. Photo 공정을 진행하면서 발생된 이물 및 불순물, 마스크 불량 등을 검사
     
    - 육안 검사(Visual Inspection)

        - 일반 광학 현미경을 이용하여 현상이 완료된 웨이퍼를 검사한다.
     
        - 반도체 칩의 크기가 작아지면서 광학 현미경으로 볼 수 없는 불량이 많다.

    - 측정 검사

        1. 정렬된 상태 측정 : 패턴의 정렬 상태를 검사
     
        2. 선폭 (CD, Critical Dimension) 측정

    - 재작업 공정

        - Photo 공정 진행 후 검사 공정에서 검사결과 다음 공정으로 진행이 불가(NG)할 경우 재작업을 실시
     
    - 재작업 방법
 
        1. Photo Layer 자체에서 진행하는 방법으로 Track 장비에서 유기용제(Thinner) 처리, 주로 재작업 해야 하는 웨이퍼의 수량이 적을 경우나 Metal Film이 있는 경우에 실시한다.
     
        2. Dry Ashing 및 세정공정(식각 후 세정 공정과 동일)으로 진행하는 방법이 있다. 대부분 재작업 시 이 방법을 사용
     
        3. Photo Layer 공정의 불량을 발견하지 못하고 다음 공정인 식각 공정이나 Implant 공정을 진행하게 되면 재작업(Rework)이 불가능하므로 올바른 판단이 중요하다.
    
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
