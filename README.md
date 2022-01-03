<h1 align="center" style="display: block; font-size: 2em; font-weight: bold; margin-block-start: 1em; margin-block-end: 1em;">
<img align="center" src="https://user-images.githubusercontent.com/91931949/147402028-87701a75-2dab-4a2a-8f1e-abd3223e6bd9.png" style="width:100%;height:100%"/>
<!--   <br><br><strong>교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발</strong> -->
</h1>


---

## Introduction
- **혼잡 여부 예측 모델**은 혼잡하지 않은 약속 장소를 원하는 서울권 거주자를 위한 모델입니다.
- 교통, 상권, 날씨 등을 고려하여, 해당 지역의 혼잡 확률을 제시하고 인근 관광지를 안내하는 Toy 서비스를 개발했습니다.
<p align="center"><img src="https://user-images.githubusercontent.com/91931949/147857092-48f1c6d6-8de6-4ff8-8e7f-e51049359d5e.gif" width="350" height="540"/></p>

---

## Contents
- [1. 프로젝트 소개](#1-프로젝트-소개)
  * [배경](#배경)
  * [프로젝트 개요](#프로젝트-개요)
- [2. 데이터 전처리](#2-데이터-전처리)
  * [Issues](#issues)
- [3. 시각화 및 분석](#3-시각화-및-분석)
- [4. 모델링](#4-모델링)
  * [모델링 개요](#모델링-개요)
  * [모델 성능 평가](#모델-성능-평가)
  * [최종 모델](#최종-모델-final-model)
  * [Toy 서비스화](#toy-서비스화)
- [5. 프로젝트 한계](#5-프로젝트-한계)
- [6. 참고 문헌](#6-참고-문헌)
- [7. 기타 사항](#7-기타-사항)

---

## 1. 프로젝트 소개
### 배경
- 우리는 지인과의 약속 장소를 정하는 일이 매번 쉽지 않고, 혼잡하지 않은 장소를 선호합니다.
- 최근 코로나 19 이슈로 인하여, 혼잡하지 않은 장소에 대한 니즈가 더욱 증가하고 있습니다.
- 약속 장소로 고려 중인 대상 지역의 혼잡 여부를 예측할 수 있다면, 위와 같은 고민이 줄어들 것입니다.
### 프로젝트 개요
1. 프로젝트명 : **교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발**
2. 수행자 : 플레이스투밋(P2M) - 이민호, 정재영, 이서영
3. 수행기간 : 2주 (2021. 12월 중)
4. 목표 : **오후 6 ~ 8시 대상 지하철역 기준 인근 지역의 혼잡 여부 제시**
5. 데이터셋 : 공공 데이터 활용, 2019-2020 총 2개년도 데이터 대상
![image](https://user-images.githubusercontent.com/91931949/147900683-deab8094-a321-464a-9e80-b02e553df6e1.png)

---

## 2. 데이터 전처리
- 총 7개 데이터셋을 전처리 수행하였고, **최종 181,499개 데이터**를 확보했습니다.
- 일별 타겟 시간대(오후 6 ~ 8시) 하차인원 데이터를 기준으로 모든 데이터셋 Merge/Join
- 전처리 과정에서 다음 **5가지 Issue에 대하여 대응·해결**했습니다.

### Issues
:pushpin: **2020년 사업체 수 데이터 정제**
- 2019년 데이터를 기준으로 업종별, 행정동별 데이터 정리

:pushpin: **행정동 통일**
- '-동' '-1동', '-1.2동', '-1가동', '-1.2가동', '-1,2가1동' 등을 기본형 '-동'으로 병합·통일

:pushpin: **오후 6~8시 날씨 전처리**
- 기온, 풍속은 평균(mean), 강수량, 적설은 누적(sum)

:pushpin: **상권수, 사업체수 결측치 처리**
- 상권수의 경우, 직접 해당 상권 존재 여부 확인하여, **있으면 해당 소속구의 median, 없으면 0**으로 처리
- 사업체수는 **해당 소속구의 median**으로 처리

:pushpin: **혼잡 여부 라벨링(Labeling)**
- 혼잡 여부를 구분하는 **혼잡 지표(산출식) 개발, 130% 이상이면 혼잡(1), 미만이면 비혼잡(0)** 으로 라벨 적용
- 혼잡 지표 = (해당 역의 하차 인원) / (해당 역의 전체 하차 인원 중간값)

---

## 3. 시각화 및 분석

:bar_chart: **Target(혼잡 여부)별 수**
<p align="left"><img src="https://user-images.githubusercontent.com/91931949/147430518-00a83676-2bf0-40ac-a536-9cd1a28d29f5.png" width="500" height="320"/></p>

- 혼잡함(1)이 10.9%, 혼잡하지 않음(0)이 89.1% 로 **매우 불균형한 분포**를 보이고 있습니다.

<br>

:bar_chart: **혼잡 여부별 날씨(기온과 풍속) 분포 – 3개 혼잡 지하철역**
<p align="left"><img src="https://user-images.githubusercontent.com/91931949/147430784-35819d24-0d44-4be0-a2e8-f91a05b801dd.png" width="950" height="250"/></p>

- '서울역'과 '종로3가역'의 경우, 기온과 풍속이 낮았던 경우에 혼잡한 것으로 나타납니다.
- 반면, **여의나루역의 경우 기온이 높을 때 혼잡**한 경우를 보이는데, 인근 한강공원의 영향, 더위를 피하는 사람들이 몰린 것으로 예상됩니다.

<br>

:bar_chart: **요일별 – 하차 인원 : 혼잡(1) / 비혼잡(0) 구분**
<p align="left"><img src="https://user-images.githubusercontent.com/56102116/147414678-25b0cc01-021d-4607-b417-5d72f490b7ae.png" width="800" height="250"/></p>

- **평일은 혼잡, 비혼잡 모두 인원 수의 변화가 거의 없는 것**으로 보입니다.
- **주말은 하차 인원 수준이 낮게** 나타나고 있습니다.
- '토요일, 혼잡 역'의 경우 이상치(Outlier)가 매우 높게 나타나고 있습니다.

<br>

:bar_chart: **Feature별 상관계수 히트맵**
<p align="left"><img src="https://user-images.githubusercontent.com/91931949/147430658-fed218d4-c2cd-49b0-a10b-eb96b81457ac.png" width="700" height="320"/></p>

- **하차인원과 사업체수, 인구수, 상권수는 높은 상관관계**를 보이고 있습니다.
- 하차인원과 기온, 풍속, 관광지수는 낮은 상관관계를 보이고 있습니다.
- 상권수와 인구수는 가장 높은 상관관계를 보입니다.

<br>

:bar_chart: **한번도 혼잡하지 않았던 역 vs 혼잡/비혼잡 공존했던 역 - 사업체수, 관광지수**
<p align="left"><img src="https://user-images.githubusercontent.com/91931949/147430969-7634b8a3-e6a3-44e7-9272-f1ef964a1972.png" width="800" height="400"/></p>

- 혼잡/비혼잡이 공존했던 역에서는 대체적으로 사업체 수가 많은 것으로 나타고 관광지 수는 오히려 적은 것으로 나타납니다.
- **유동 인구는 출퇴근의 영향**이 크다는 것을 확인할 수 있습니다.

<br>

:bar_chart: **한번도 혼잡하지 않았던 역 vs 혼잡/비혼잡 공존했던 역 - 기온**
<p align="left"><img src="https://user-images.githubusercontent.com/91931949/147431026-a56d8380-0686-4d37-a8e5-19bb8bd0c936.png" width="800" height="400"/></p>

- 혼잡하지 않았던 경우는 혼잡했던 경우에 비해 기온의 차이에 따라 분포의 차이가 큰 것으로 나타납니다.
- 영하 10도 이하의 매우 추운 날의 경우, 혼잡한 경우가 없는 것으로 나타나고 있습니다.

<br>

---

## 4. 모델링

### 모델링 개요
:mag:**모델 기본 설계 (Model Design)**
<img src="https://user-images.githubusercontent.com/91931949/147431095-9dcc7793-4eba-4b39-b0ce-e86bec509304.png" width="800" height="310"/>

<br>

:mag:**특성 선택 (Feature Selection)**
- 각 Case별로 Feature Selection 진행하고, 각 모델의 결과를 확인했습니다.
<img src="https://user-images.githubusercontent.com/56102116/147429181-4b450551-1ea4-4f9a-94b0-5b198a8f5631.png" width="800" height="270"/>

<br>

### 모델 성능 평가
:point_right: **Case 1 : 모든 특성 선택** 

<img src="https://user-images.githubusercontent.com/91931949/147900524-cd0947ea-2489-437a-97b1-85d17eba55d9.png" width="500" height="300"/>

- Best Model: **RandomForest Classifier**

<img src="https://user-images.githubusercontent.com/91931949/147900537-b396cc49-5f76-425e-84e8-5efc1e7263fe.png" width="800" height="500"/>

- **Acccuracy: 0.957, F1 Score: 0.646**
 
<br>
 
:point_right: **Case 2 : 적설, 강수량 제외**

<img src="https://user-images.githubusercontent.com/91931949/147900560-79b5769b-84dc-4d8e-9849-5d4b766b67d7.png" width="500" height="300"/>

- Best Model: **RandomForest Classifier**

<img src="https://user-images.githubusercontent.com/91931949/147900567-0aa5e5b4-d05c-407b-8e64-236ee1291488.png" width="800" height="500"/>

- Case 1(RandomForest)에서 Feature Importance가 가장 낮았던 **적설, 강수량을 제외**했습니다.
- **Case 1 대비 F1 Score 0.013 하락**

<br>

:point_right: **Case 3 : 관광지수, 상권수 제외**

<img src="https://user-images.githubusercontent.com/91931949/147900584-480d0c74-6d31-4a34-8c34-02c7f51e4fc2.png" width="500" height="300"/>

- Best Model: **RandomForest Classifier**

<img src="https://user-images.githubusercontent.com/91931949/147900591-a1278444-1eb2-4f33-9ea4-da071a16d0e8.png" width="800" height="500"/>

- Case 1에서 변동성이 없는 데이터 중 중요도가 낮은 2개(관광지수, 상권수)를 제외했습니다.
- **Case 1(모든 변수)와 비교했을 때, Accuracy(+0.002)와 F1 Score(+0.007) 모두 상승했습니다.**
- **즉, '관광지수와 상권수'가 모델의 성능을 오히려 저하할 수 있다는 판단을 내릴 수 있습니다.**

<br>

:point_right: **Case 4 : 요일 제외**

<img src="https://user-images.githubusercontent.com/91931949/147900613-4b5f47b8-9bb4-4c42-b1dd-f972cdfc115c.png" width="500" height="300"/>

- Best Model: **LGBM Classifier**

<img src="https://user-images.githubusercontent.com/91931949/147900618-581d717d-10c1-4bab-b907-ac35ba2de42c.png" width="800" height="500"/>

- **요일별 지하철 이용 목적이 크게 달라지기 때문에, '요일'특성이 매우 중요한 영향을 미칩니다.**

<br>

:point_right: **Case 5 : 날씨 전체 제외**

<img src="https://user-images.githubusercontent.com/91931949/147900643-133f931d-777b-47a0-901e-035e3a8bc663.png" width="500" height="300"/>

- Best Model: **RandomForest Classifier**

<img src="https://user-images.githubusercontent.com/91931949/147900649-f1d5138f-412e-41bd-b377-74a6fff37c8f.png" width="800" height="500"/>

- **날씨 특성 역시 모델의 성능에 큰 영향을 주고 있습니다.**

<br>

### 최종 모델 (Final Model)
|Title|Contents|
|:------:|:---:|
|**Classifier**|:loudspeaker: **RadomForestClassifier (Standard Scaler)**|
|**Feature**|:loudspeaker: **Case3 : 관광지 수, 상권 수 제외**|
|**Score**|Acc : 0.959, F1 : 0.653|
|**Test**|Test dataset 36,300개 중 1,541개 예측 틀림|
|**Reason**|- 가장 높은 Accuracy, F1 Score</br>- 예상과 다르게, 역 주변 관광지 수와 상권 수는 모델에 미치는 영향이 크지 않음</br>- 하차 인원에 따른 혼잡여부는, 방문인원보다 근무자의 퇴근에 영향을 받음</br>- **6 ~ 8시 만남을 위한 이동보다는, 대중교통 퇴근을 위한 이동을 고려해야 함** </br>|

<br>

### Toy 서비스화
- **유저가 우리 모델을 활용**할 수 있도록 간단한 Toy 서비스를 구현·개발했습니다.
> 1. 내가 가려는 지하철역 인근의 혼잡할 확률 제시 <br>
> 2. 해당 인근 지역의 관광지를 함께 제시 <br>

- 서비스화를 위한 **get_congestion 함수**를 정의했습니다. <br>

  * *get_congestion(요일, 기온, 강수량, 적설, 풍속, 역명, 호선)*
  * 각 Feature값을 넣으면 **혼잡할 확률과 인근관광지**가 출력
  ![image](https://user-images.githubusercontent.com/91931949/147433122-839de4bf-f24d-4136-933b-26fc976e5728.png)

---

## 5. 프로젝트 한계
:bulb: **Cold Start Problem**

- 코로나 이슈로 인한 데이터 부족

    * 2020년 이전과 이후에 코로나19로 인한 데이터 양상이 매우 상이하게 나타났습니다.
    * 시계열 고려 없이, **5년치 데이터 사용 시, 코로나19로 인한 변화가 반영되지 않을 것**으로 판단하여, 2년치만 사용했습니다.
    * 최근 경향이 반영된 데이터가 부족한 것이 프로젝트의 한계라고 느꼈습니다.

- 사업체 수 등 변화 반영 미흡

    * 코로나 이슈로 가장 큰 변화가 있었던 것은 '사업체 수'의 변화입니다.
    * **데이터 제공자의 업데이트 지연**으로, 해당 부분의 변화를 상당 부분 반영하지 못한 점이 있었습니다.

<br>

:bulb: **라벨링(Labeling) 문제**

- 혼잡 여부 산출식의 주관성

    * 혼잡 여부 라벨을 위한 혼잡 지표를 개발했지만, 유사 연구가 없어서 **올바른 지표인지 판단할 기준이 부재**합니다.
    * 지하철 내부 혼잡도 측정 연구, 통신 데이터 기반 유동인구 혼잡 지표 개발 연구 등을 참고했지만,
    * **직접적으로 '하차인원'을 활용한 지하철역 인근의 혼잡을 판단하는 연구는 존재하지 않았습니다.**

- 혼잡 여부에 영향을 미치는 추가 데이터 확보 필요성

    * 혼잡 여부를 라벨링하는 과정에서 **'하차인원'만을 고려**했습니다.
    * 혼잡을 표현할 수 있는 다른 데이터가 있다면, 더욱 효과적인 라벨링을 진행할 수 있겠다는 생각을 했습니다.

<br>

:bulb: **데이터 불균형**

- 데이터 통일성 부족

    * **데이터 제공자의 형식 통일 미흡**으로, 전처리를 위한 추가적인 비용이 소요되었습니다.

- 결측치 처리에 따른 성능 저하 

    * 특정 지하철 역의 **하차인원 데이터가 결측**되어 있는 경우 (Ex. 연신내, 충무로역 1년치 데이터 누락)
    * 지하철역 매칭(matching) 시 **사업체 수, 상권 수 등 데이터 결측**되어 있는 경우

- 특정 지하철역의 극단적인 이상값(Outlier)의 쏠림

    * 혼잡 비율의 Outlier를 모두 제거하면 **신림역 등의 특정 역의 데이터 절반 이상이 제거되는 현상**을 보였습니다.
    * 따라서, 혼잡 비율의 아웃라이어는 제거하지 않고 그대로 모델링을 진행하였으며,
    * 해당 부분이 **데이터 불균형을 초래**할 것으로 예상됩니다. 

<br>

:bulb: **Accuracy 대비 낮은 F1 Score**

- 타겟(Target) 불균형 현상

    * 설계한 산출식에 따라 도출된 혼잡(1) 라벨이 매우 적게 나타났습니다.
    * Upsampling(SMOTE)을 통해 개선했지만, **타겟 불균형이 모델에 영향을 끼쳤을 가능성이 높다고 판단**됩니다.
    
- F1 Score를 높일 수 있는 추가적인 데이터 수집 필요성

    * Accuracy에 비하여 **F1 Score가 낮게** 나타났습니다.
    * F1 개선을 위하여 **행정동의 면적, 지하철 역의 면적 등 추가적인 데이터 수집의 필요성**을 느꼈습니다.
 
---

## 6. 참고 문헌
- 대도시권광역교통위원회(2019), 도시철도의 건설과 지원에 관한 기준
- 김진수(2016), 빅데이터 분석을 이용한 지하철 혼잡도 예측 및 추천시스템
- 김해 외(2018), 유동인구 빅데이터 기반 고속도로 휴게소 혼잡지표 개발 연구
- 김진아 외(2020), 빅데이터 분석을 활용한 공항 혼잡도 분석 - 김포공항 사례를 중심으로
- 서울교통공사(2019), 신형 전동차 혼잡도 표시 기준

---

## 7. 기타 사항
**Team members**
|Member|See more!|
|:--:|:--:|
|이민호 (Minho Lee)|<a href="mailto:minho5373@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/minho5373"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://whimsical-argon-e67.notion.site/Minho-Lee-7fd5586ba1dc4ed5b30964f76adf6417"><img src="https://img.shields.io/badge/Notion-grey?style=flat-sqaure&logo=Notion&logoColor=ffffff"/></a> <a href="https://go-for-data.tistory.com/"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
|이서영 (Seoyoung Lee)|<a href="mailto:cococindy98@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/cococindy98"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://shaded-english-cde.notion.site/Seoyoung-Lee-6c8d2a44a3bd48dbb30256cbfa83c485"><img src="https://img.shields.io/badge/Notion-grey?style=flat-sqaure&logo=Notion&logoColor=ffffff"/></a> <a href="https://blog.naver.com/cococindy98"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
|정재영 (Jaeyoung Jeong)|<a href="mailto:wodud921@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/jaeyoung9849"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://blog.naver.com/wodud921"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
