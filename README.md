<h1 align="center" style="display: block; font-size: 2em; font-weight: bold; margin-block-start: 1em; margin-block-end: 1em;">
<img align="center" src="https://user-images.githubusercontent.com/91931949/147402028-87701a75-2dab-4a2a-8f1e-abd3223e6bd9.png" style="width:100%;height:100%"/>
<!--   <br><br><strong>교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발</strong> -->
</h1>

# 교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발

## 📋 Introduction
- 최근 코로나 19 이슈로 인하여, 혼잡하지 않은 장소에 대한 니즈가 더욱 증가하고 있습니다.
- **혼잡 여부 예측 모델**은 혼잡하지 않은 약속 장소를 원하는 서울권 거주자를 위한 모델입니다.
- 교통, 상권, 날씨 등을 고려하여, **오후 6-8시 시간대에 해당 지역의 혼잡 확률을 제시, 인근 관광지를 안내**하는 Toy 서비스를 개발했습니다.
<p align="center"><img src="https://user-images.githubusercontent.com/91931949/147857092-48f1c6d6-8de6-4ff8-8e7f-e51049359d5e.gif" width="320" height="500"/></p>


<!-- ## Contents
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

--- -->

## 🎯 Result
우리의 최종 모델은 **Accuracy 0.959, F1 Score 0.653**의 성능을 보입니다.
<p align="center"><img src="https://user-images.githubusercontent.com/91931949/147900591-a1278444-1eb2-4f33-9ea4-da071a16d0e8.png" width="700" height="400"/>

## 🔎 Modeling
### 1. Dataset
- 공공 데이터 활용, 2019-2020 총 2개년도 데이터 대상
- 총 7개 데이터셋을 전처리 수행, **최종 181,499개 데이터**를 확보
<p align="center"><img src="https://user-images.githubusercontent.com/91931949/147900683-deab8094-a321-464a-9e80-b02e553df6e1.png" width="700" height="180"/></p>

### 2. Library
- scikit-learn을 활용하여 ML-pipeline을 설계했습니다.
```
!pip install scikit-learn
```

### 3. Preprocessing
- **2020년 사업체 수 데이터 정제**
```
  - 2019년 데이터를 기준으로 업종별, 행정동별 데이터 정리
```
- **행정동 통일**
```
  - '-동' '-1동', '-1.2동', '-1가동', '-1.2가동', '-1,2가1동' 등을 기본형 '-동'으로 병합·통일
```
- **오후 6~8시 날씨 전처리**
```
  - 기온, 풍속은 평균(mean), 강수량, 적설은 누적(sum)
```
- **상권수, 사업체수 결측치 처리**
```
  - 상권수의 경우, 직접 해당 상권 존재 여부 확인하여, '있으면 해당 소속구의 median, 없으면 0'으로 처리
  - 사업체수는 '해당 소속구의 median'으로 처리
```
- **혼잡 여부 라벨링(Labeling)**
```
  - 혼잡 여부를 구분하는 '혼잡 지표(산출식) 개발, 130% 이상이면 혼잡(1), 미만이면 비혼잡(0)'으로 라벨 적용
  - 혼잡 지표 = (해당 역의 하차 인원) / (해당 역의 전체 하차 인원 중간값)
```

### 4. EDA & Insight
|Analysis|Insight|
|:------|:------|
|비혼잡 : 혼잡 = 9:1로 나타남| → 타겟 불균형으로 Oversampling 진행|
|소수의 지하철역에서 하차인원이 집중됨| → Labeling 지표를 전체가 아닌 '해당 지하철역' 기준으로 수정|
|평일과 주말 간 데이터의 뚜렷한 차이를 보임| → 모델에 출퇴근 유동인구의 영향력이 반영될 것|
|날씨 변수보다 지역적 특성변수의 영향이 크게 나타남| → Case별 Feature Selection 필요|

### 5. Model setting
- **6개 Classifier를 포함한 Pipeline을 구성**하여 모델 성능을 비교했습니다.
<img src="https://user-images.githubusercontent.com/91931949/147431095-9dcc7793-4eba-4b39-b0ce-e86bec509304.png" width="600" height="250"/>

- 각 Case별로 **Feature Selection 진행**하고, 각 모델의 결과를 확인했습니다.
<img src="https://user-images.githubusercontent.com/56102116/147429181-4b450551-1ea4-4f9a-94b0-5b198a8f5631.png" width="600" height="200"/>

- **Final Model**

|Title|Contents|
|:------:|:---:|
|**Classifier**|:loudspeaker: **RadomForestClassifier (Standard Scaler)**|
|**Feature**|:loudspeaker: **Case3 : 관광지 수, 상권 수 제외**|
|**Score**|Acc : 0.959, F1 : 0.653|
|**Test**|Test dataset 36,300개 중 1,541개 예측 틀림|
|**Reason**|- 가장 높은 Accuracy, F1 Score</br>- 예상과 다르게, 역 주변 관광지 수와 상권 수는 모델에 미치는 영향이 크지 않음</br>- 하차 인원에 따른 혼잡여부는, 방문인원보다 근무자의 퇴근에 영향을 받음</br>- **6 ~ 8시 만남을 위한 이동보다는, 대중교통 퇴근을 위한 이동을 고려해야 함** </br>|

<br>

## :jigsaw: Toy Service
- **유저가 우리 모델을 활용**할 수 있도록 간단한 Toy 서비스를 구현·개발했습니다.
```
1. 내가 가려는 지하철역 인근의 혼잡할 확률 제시
2. 해당 인근 지역의 관광지를 함께 제시
```

- 서비스화를 위한 **get_congestion 함수**를 정의했습니다. <br>

  * *get_congestion(요일, 기온, 강수량, 적설, 풍속, 역명, 호선)*
  * 각 Feature값을 넣으면 **혼잡할 확률과 인근관광지**가 출력
  ![image](https://user-images.githubusercontent.com/91931949/147433122-839de4bf-f24d-4136-933b-26fc976e5728.png)

## :gear: Limitations
### 1. 라벨링(Labeling) 문제

- 혼잡 여부 산출식의 주관성

    * 혼잡 여부 라벨을 위한 혼잡 지표를 개발했지만, 유사 연구가 없어서 **올바른 지표인지 판단할 기준이 부재**합니다.
    * 지하철 내부 혼잡도 측정 연구, 통신 데이터 기반 유동인구 혼잡 지표 개발 연구 등을 참고했지만,
    * **직접적으로 '하차인원'을 활용한 지하철역 인근의 혼잡을 판단하는 연구는 존재하지 않았습니다.**

### 2. 데이터 불균형 문제

- 결측치 처리에 따른 성능 저하 

    * 특정 지하철 역의 **하차인원 데이터가 결측**되어 있는 경우 (Ex. 연신내, 충무로역 1년치 데이터 누락)
    * 지하철역 매칭(matching) 시 **사업체 수, 상권 수 등 데이터 결측**되어 있는 경우

- 특정 지하철역의 극단적인 이상값(Outlier)의 쏠림

    * 혼잡 비율의 Outlier를 모두 제거하면 **신림역 등의 특정 역의 데이터 절반 이상이 제거되는 현상**을 보였습니다.
    * 따라서, 혼잡 비율의 아웃라이어는 제거하지 않고 그대로 모델링을 진행하였으며,
    * 해당 부분이 **데이터 불균형을 초래**할 것으로 예상됩니다. 

### 3. Accuracy 대비 낮은 F1 Score

- 타겟(Target) 불균형 현상

    * 설계한 산출식에 따라 도출된 혼잡(1) 라벨이 매우 적게 나타났습니다.
    * Upsampling(SMOTE)을 통해 개선했지만, **타겟 불균형이 모델에 영향을 끼쳤을 가능성이 높다고 판단**됩니다.
 

## 📖 Reference
- 대도시권광역교통위원회(2019), 도시철도의 건설과 지원에 관한 기준
- 김진수(2016), 빅데이터 분석을 이용한 지하철 혼잡도 예측 및 추천시스템
- 김해 외(2018), 유동인구 빅데이터 기반 고속도로 휴게소 혼잡지표 개발 연구
- 김진아 외(2020), 빅데이터 분석을 활용한 공항 혼잡도 분석 - 김포공항 사례를 중심으로
- 서울교통공사(2019), 신형 전동차 혼잡도 표시 기준

## :palms_up_together: Team Members
|Member|See more!|
|:--:|:--:|
|이민호 (Minho Lee)|<a href="mailto:minho5373@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/minho5373"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://whimsical-argon-e67.notion.site/Minho-Lee-7fd5586ba1dc4ed5b30964f76adf6417"><img src="https://img.shields.io/badge/Notion-grey?style=flat-sqaure&logo=Notion&logoColor=ffffff"/></a> <a href="https://go-for-data.tistory.com/"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
|이서영 (Seoyoung Lee)|<a href="mailto:cococindy98@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/cococindy98"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://shaded-english-cde.notion.site/Seoyoung-Lee-6c8d2a44a3bd48dbb30256cbfa83c485"><img src="https://img.shields.io/badge/Notion-grey?style=flat-sqaure&logo=Notion&logoColor=ffffff"/></a> <a href="https://blog.naver.com/cococindy98"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
|정재영 (Jaeyoung Jeong)|<a href="mailto:wodud921@gmail.com"><img src="https://img.shields.io/badge/Mail-red?style=flat-sqaure&logo=Gmail&logoColor=ffffff"/></a> <a href="https://github.com/jaeyoung9849"><img src="https://img.shields.io/badge/Github-black?style=flat-sqaure&logo=GitHub&logoColor=ffffff"/></a> <a href="https://blog.naver.com/wodud921"><img src="https://img.shields.io/badge/Blog-green?style=flat-sqaure&logo=Storyblok&logoColor=ffffff"/></a>|
