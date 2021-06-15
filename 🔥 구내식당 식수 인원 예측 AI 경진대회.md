# 🔥 [구내식당 식수 인원 예측 AI 경진대회](https://dacon.io/competitions/official/235743/overview/description)

2021.06.03 ~ 2021.07.23 / 참여 인원 : 허정훈, 장준보, 류현주, 우일

구내식당의 요일별 점심, 저녁식사를 먹는 인원을 예측 → 점심, 저녁 다른 모델을 사용할 것

train : 2016-02-01 ~ 2021-01-26 / test : 2021-01-27 ~ 2021-04-19 경상남도 진주시

## 💻Data Info

* 일자, 요일, 본사정원수, 본사휴가자수, 본사출장자수, 본사시간외근무명령서승인건수, 현본사소속재택근무자수, 조식메뉴, 중식메뉴, 석식메뉴, 중식계, 석식계로 이루어져 있음.
  * 일자, 요일, 정원, 휴가자, 출장자, 야근자, 재택근무자, 조식, 중식, 석식, 중식계, 석식계로
  * **메뉴를 제외한 변수**부터 작업해볼 예정

## 🌏 외부 데이터

### 🧐 활용 계획

* 코로나 데이터 → 고민
* 날씨 데이터 → 강수량을 명목변수로 사용, 기온-습도 → 불쾌지수 명목변수
* 미세먼지 데이터 → 명목변수
* 공휴일 데이터 → 공휴일 전후 변수 추가

###  😇 데이터 출처

* 코로나 발생현황 데이터 [link](https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15043378) (국내, 경남, 진주시)

  * 일일확진자, 누적확진자, 일일검사자 데이터

* 날씨 데이터 [link](https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15057682) (진주시 충무공동 데이터)

  * 강수량, 강수형태, 기온, 습도, 불쾌지수 데이터 → 점심-저녁 분류

    * 강수형태 : 없음(0), 비(1), 비/눈(2), 눈(3), 소나기(4), 빗방울(5), 빗방울/눈날림(6), 눈날림(7) → 비/눈은 비와 눈이 섞여 오는 것을 의미 (진눈개비) [repo](http://www.riss.kr/search/detail/DetailView.do?p_mat_type=be54d9b8bc7cdb09&control_no=b50f3c1b6f3af86cffe0bdc3ef48d419)

      ![image-20210615151529085](img/image-20210615151529085.png)

    * 불쾌지수 = 1.8x기온 - 0.55x(1-습도)x(1.8x기온-26)+32 [ref](https://www.kma.go.kr/HELP/basic/help_01_05.jsp)

      ![image-20210615151349873](img/image-20210615151349873.png)

* 미세먼지 데이터 [2016-2020](http://www.airkorea.or.kr/web/pastSearch?pMENU_NO=123) [2021](http://www.airkorea.or.kr/web/realSearch?pMENU_NO=97) (경남 진주시 동진로 279 (한국전력공사 진주지점))

  * PM10 : 미세먼지, PM2.5 : 초미세먼지
  * 결측치는 가장 가까운 다른 지점 관측치로 대체
  * 아래의 기준에 따라 명목변수로 분류

  ![image-20210615150003992](img/image-20210615150003992.png)

  ![image-20210615150031974](img/image-20210615150031974.png)

* 휴일(공휴일/대체공휴일) 데이터 [link](https://www.data.go.kr/data/15012690/openapi.do)

## 🤔 고민점

* 복날 데이터를 넣을까? 너무 전체 데이터에 비해 적다. → 메뉴로 대체할 수 있지 않을까?
  * '2017-07-12', '2018-07-17', '2019-07-12', '2020-07-16'
* 연말 데이터는 또 어떤가? 너무 Sparse 할까봐 고민

## 📑참고 논문

[머신러닝을 이용한 단체급식 다중코너 식수 예측 모델 연구(2021)](http://www.riss.kr/search/detail/DetailView.do?p_mat_type=be54d9b8bc7cdb09&control_no=b50f3c1b6f3af86cffe0bdc3ef48d419)

[기계학습방법을 활용한 대형 집단급식소의 식수 예측(2019)](http://koreascience.or.kr/article/JAKO201912261946958.pdf)

 



