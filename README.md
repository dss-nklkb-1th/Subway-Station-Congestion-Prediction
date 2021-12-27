<h1 align="center" style="display: block; font-size: 2em; font-weight: bold; margin-block-start: 1em; margin-block-end: 1em;">
<img align="center" src="https://user-images.githubusercontent.com/91931949/147402028-87701a75-2dab-4a2a-8f1e-abd3223e6bd9.png" style="width:100%;height:100%"/>
<!--   <br><br><strong>교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발</strong> -->
</h1>


---

## Introduction
- **혼잡 여부 예측 모델**은 혼잡하지 않은 약속 장소를 원하는 서울권 거주자를 위한 모델입니다.
- 교통, 상권, 날씨 등을 고려하여, 해당 지역의 혼잡 확률을 제시하고 인근 관광지를 안내하는 Toy 서비스를 개발했습니다.

---

## Contents
- [프로젝트 소개](#1-프로젝트-소개)
  * [배경](#배경)
  * [프로젝트 개요](#프로젝트-개요)
- [상세 내용](#2-상세-내용)
  * [데이터 전처리](#데이터-전처리)
  * [시각화 및 분석](#시각화-및-분석)
  * [모델링 결과](#모델링-결과)
  * [Toy 서비스화](#toy-서비스화)
- [참고 문헌](#3-참고-문헌)
- [기타 사항](#4-기타-사항)

---

## 1. 프로젝트 소개
### 배경
- 우리는 지인과의 약속 장소를 정하는 일이 매번 쉽지 않고, 혼잡하지 않은 장소를 선호합니다.
- 최근 코로나 19 이슈로 인하여, 혼잡하지 않은 장소에 대한 니즈가 더욱 증가하고 있습니다.
- 약속 장소로 고려 중인 대상 지역의 혼잡 여부를 예측할 수 있다면, 위와 같은 고민이 줄어들 것입니다.
### 프로젝트 개요
1. 프로젝트명 : 교통, 상권, 날씨를 고려한 혼잡 여부 예측 모델 개발
2. 수행자 : 플레이스투밋(P2M) - 이민호, 정재영, 이서영
3. 수행기간 : 2주 (2021. 12월 중)
4. 목표 : 오후 6 ~ 8시 대상 지하철역 기준 인근 지역의 혼잡 여부 및 해당 확률 제시
5. 데이터셋 : 공공 데이터 활용, 2019-2020 총 2개년도 데이터 대상
![image](https://user-images.githubusercontent.com/91931949/147322577-44cdfefe-7c18-4a71-a417-168044e9b7dc.png)

## 2. 상세 내용
### 데이터 전처리
- 총 7개 데이터셋을 전처리 수행하였고, **최종 181,499개 데이터**를 확보했습니다.
- 일별 타겟 시간대(오후 6 ~ 8시) 하차인원 데이터를 기준으로 모든 데이터셋 Merge/Join
- 전처리 과정에서 다음 5가지 Issue에 대하여 대응·해결했습니다.

#### Issues:
:pushpin:**2020년 사업체 수 데이터 정제**
- 2019년 데이터를 기준으로 업종별, 행정동별 데이터 정리

:pushpin:**행정동 통일**
- '-동' '-1동', '-1.2동', '-1가동', '-1.2가동', '-1,2가1동' 등을 기본형 '-동'으로 병합·통일

:pushpin:**오후 6~8시 날씨 전처리**
- 기온, 풍속은 평균(mean), 강수량, 적설은 누적(sum)

:pushpin:**상권수, 사업체수 결측치 처리**
- 상권수의 경우, 직접 해당 상권 존재 여부 확인하여, **있으면 해당 소속구의 median, 없으면 0**으로 처리
- 사업체수는 **해당 소속구의 median**으로 처리

:pushpin:**혼잡 여부 라벨링(Labeling)**
- 혼잡 여부를 구분하는 **혼잡 지표(산출식) 개발, 130% 이상이면 혼잡(1), 미만이면 비혼잡(0)** 으로 라벨 적용
- 혼잡 지표 = (해당 역의 하차 인원) / (해당 역의 전체 하차 인원 중간값)


### 시각화 및 분석
:art:Target(혼잡 여부)별 수
![image](https://user-images.githubusercontent.com/56102116/147411699-0442e153-d97a-429e-9c6b-a5f2e2d0cd9f.png)
- 혼잡함(1)이 10.9%, 혼잡하지 않음(0)이 89.1% 로 매우 불균형한 분포를 나타남

---
:art:혼잡 여부별 날씨(기온과 풍속) 분포 – 3개 혼잡 지하철역
![image](https://user-images.githubusercontent.com/56102116/147414189-5aba2048-aaea-4923-8a51-3ae2002d2a1c.png)
- 서울역과 종로3가역의 경우 다소 기온과 풍속이 낮았던 경우에 혼잡한 것으로 나타남
- 여의나루역의 경우 기온이 높을때 혼잡한 경우를 보였는데 한강공원 바로 옆에 위치하여 더위를 피하는 사람들이 몰린것으로 예상됨

---
:art:요일별 – 하차 인원 : 혼잡(1) / 비혼잡(0) 구분
![image](https://user-images.githubusercontent.com/56102116/147414678-25b0cc01-021d-4607-b417-5d72f490b7ae.png)
- 평일은 혼잡함, 비혼잡함 모두 인원 수의 변화가 거의 없음
- 주말의 경우 하차 인원의 수준이 낮게 나타남
- 토요일 혼잡 역의 경우 이상치가 매우 높게 나타남

---
:art:Feature별 상관계수 히트맵
![image](https://user-images.githubusercontent.com/56102116/147415010-8a50222a-21ff-46d4-bbe9-646d0c1e54fc.png)
- 하차인원과 사업체수, 인구수, 상권수는 높은 상관관계를 보임
- 하차인원과 기온, 풍속, 관광지수는 낮은 상관관계를 보임
- 상권수와 인구수는 가낭 높은 상관관계를 보임

---
:art:한번도 혼잡하지 않았던 역 vs 혼잡/비혼잡 공존했던 역 - 사업체수, 관광지수
![image](https://user-images.githubusercontent.com/56102116/147415053-2e2a2574-67c1-4815-9f67-fcc2f27bcd56.png)
- 혼잡/비혼잡이 공존했던 역에서는 대체적으로 사업체수가 많은 것으로 나타고 관광지수는 오히려 적은 것으로 나타남
- 유동인구는 출퇴근의 영향이 크다는 것을 확인

---
:art:한번도 혼잡하지 않았던 역 vs 혼잡/비혼잡 공존했던 역 - 기온
![image](https://user-images.githubusercontent.com/56102116/147415171-525e1257-a515-49aa-9066-9b15c262df4a.png)
- 혼잡하지 않았던 경우는 혼잡했던 경우에 비해 기온의 차이에 따라 분포의 차이가 커짐
- -10도 이하의 매우 추운날의 경우 혼잡한 경우가 없었음

---



### 모델링 결과
:lollipop:모델 기본 설계(Model Design)
![image](https://user-images.githubusercontent.com/56102116/147415238-fdb93f61-dfc5-44c3-bb86-4e98fae44267.png)
- Feature간의 단위의 차이를 없에주기 위해 Standard Scaler 사용
- 총 6개의 분류기 사용
- 라벨의 불균형(약 9대1)을 해소하기 위해 Upsampling기법 적용
- Train set, Validation Set, Test set의 비율 64대16대20

---
:lollipop:특성 선택(Feature Selection)
![image](https://user-images.githubusercontent.com/56102116/147415327-764f7c25-a428-4a49-b899-524273619013.png)
- 총 5가지 Case로 나누어 비교분석 

---

### 모델 성능 평가 (Score)
👉 **Case1 : 모든 특성 선택** 
|Models|Feature Importance|
|:-:|:-:|
|![Models](https://user-images.githubusercontent.com/83954540/147422120-63d90346-d4b7-450a-810b-4a397ccf3380.png)|![Feature Importance](https://user-images.githubusercontent.com/83954540/147421326-3b097e3b-b719-417a-935a-31d463fb1aed.png)|
- **Acccuracy : 0.957, F1 Score : 0.646**
  
<br>
 
👉 **Case2 : 적설, 강수량 제외**
|Models|Feature Importance|
|:-:|:-:|
|![Models](https://user-images.githubusercontent.com/83954540/147421435-ca263b58-5a0f-421f-9acb-638865eec856.png)|![Feature Importance](https://user-images.githubusercontent.com/83954540/147421442-05d2e4a3-1d0f-483f-8e5d-9e8a5c4a6ae1.png)|
- Case1(RF기준)에서 피처중요도가 가장 낮았던 적설,강수량을 제외
- **Case1대비 F1 Score 0.013하락**

<br>

👉 **Case3 : 관광지수, 상권수 제외**
|Models|Feature Importance|
|:-:|:-:|
|![Models](https://user-images.githubusercontent.com/83954540/147421541-bc705cbf-1401-47b8-b7ec-724c6cde909a.png)|![Feature Importance](https://user-images.githubusercontent.com/83954540/147421550-3b062697-8f8f-4180-b632-f48967296eff.png)|
- Case1에서 변동성이 없는 데이터 중 중요도가 낮은 2개(관광지수, 상권수) 제외
- **Case1(모든 변수)와 비교시, Accuracy(+0.002)와 F1 Score(+0.007) 모두 상승했다.**
- **즉, '관광지수와 상권수'가 모델의 성능을 오히려 저하할 수도 있다는 판단을 내릴 수 있다.**
  
<br>

👉 **Case4 : 요일 제외**
|Models|Feature Importance|
|:-:|:-:|
|![Models](https://user-images.githubusercontent.com/83954540/147421632-96fe64f2-0d2b-417e-a9d6-26e10fd37a13.png)|![Feature Importance](https://user-images.githubusercontent.com/83954540/147421638-00ba51bb-a730-4840-9dfe-c151d92ddded.png)|
- **요일별 지하철 이용 목적이 크게 달라지기 때문에, '요일'특성이 매우 중요한 영향을 미친다.**


<br>

👉 **Case5 : 날씨 전체 제외**
|Models|Feature Importance|
|:-:|:-:|
|![Models](https://user-images.githubusercontent.com/83954540/147421742-e95db35f-8104-495e-b902-44f3d68a3660.png)|![Feature Importance](https://user-images.githubusercontent.com/83954540/147421751-dba73890-b748-42bb-b5b5-c9680b1f9af4.png)|
- **날씨 특성 역시 모델의 성능에 영향을 주고 있다.**

<br>

### 📢최종모델 (Final Model)
|Title|Contents|
|------|---|
|**Classifier**|RadomForestClassifier (Standard Scaler)|
|**Feature**|**Case3 : 관광지수, 상권수 제외**|
|**Score**|Acc : 0.959, F1 : 0.653|
|**Reason**|- Acc, F1의 가장 높은 점수</br>- 예상과 다르게, 역 주변 관광지 수와 상권 수는 모델에 미치는 영향이 크지 않다고 판단</br>- 하차 인원에 따른 혼잡여부는, 방문인원보다 근무자의 퇴근에 영향을 받음</br>- **6~8시 만남을 위한 이동보다는, 대중교통 퇴근을 위한 이동을 고려해야 함** </br>|
|**Test**|36,300개 중 1,541개 틀림|


### Toy 서비스화
✔ **프로젝트 목표 : "내가 가려는 지하철역 인근이 혼잡할지 예측한다."**
- 내가 가려는 지하철역 인근의 혼잡할 확률 제시
- 해당 인근 지역의 관광지를 함께 제시 
- 서비스화를 위한 get_congestion 함수 정의 
  * *get_congestion(요일, 기온, 강수량, 적설, 풍속, 역명, 호선)*
  *  각 Feature값을 넣으면 **혼잡할 확률과 인근관광지**가 출력됨
  ![image](https://user-images.githubusercontent.com/83954540/147421992-3bbba69b-4663-447c-857a-73f09cf7d0d2.png)

---

### 프로젝트 한계점
1. Cold Start Problem
- 위드코로나 데이터 부족
- 사업체수 등 변화 반영 미흡
2. 라벨링 문제
- 혼잡 여부 산출식의 주관성
3. 데이터 불균형
- 데이터 통일성 부족
- 결측치 처리에 따른 성능 저하 

## 3. 참고 문헌
- 대도시권광역교통위원회(2019), 도시철도의 건설과 지원에 관한 기준
- 김진수(2016), 빅데이터 분석을 이용한 지하철 혼잡도 예측 및 추천시스템
- 김해 외(2018), 유동인구 빅데이터 기반 고속도로 휴게소 혼잡지표 개발 연구
- 김진아 외(2020), 빅데이터 분석을 활용한 공항 혼잡도 분석 - 김포공항 사례를 중심으로
- 서울교통공사(2019), 신형 전동차 혼잡도 표시 기준

## 4. 기타 사항
**Team member**
- 이민호 [Github](https://github.com/minho5373)
- 이서영 [Github](https://github.com/cococindy98)
- 정재영 [Github](https://github.com/jaeyoung9849)
