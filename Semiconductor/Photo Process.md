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

- 도포(P.R 코팅)

    - PR(Photo Resist)을 Wafer의 전면에 고르게 덮는 공정으로 Track 장비에서 이루어지고, Spin Spray 방식이다

    - Adhesion(접착) : Wafer 위에 PR이 잘 붙도록 "화학처리"를 해주는 공정(풀 역할)

    - Track 장비에서 가압시킨 N2 가스가 HMDS를 이동시키는 기상도포방식
 
    - HMDS(Hexa Methylene DiSiloxane) : Wafer Silicon과 화학반응을 일으켜 접착성을 향상시키는 역할을 한다.

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
