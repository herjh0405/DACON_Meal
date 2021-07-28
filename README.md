# [[DACON] LH 구내식당 식수 인원 예측 대회](https://dacon.io/competitions/official/235743/overview/description)

---

![image-20210727220754178](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210727220754178.png)

## 🎁 개요

[2021.06.03 ~ 2021.07.23]
 신용도 예측 대회가 끝나고 다음 대회를 물색하던 중 굉장히 흥미로운 대회가 개최되었습니다. 한국토지주택공사, 즉 LH의 구내식당 식수 인원을 예측하는 대회였습니다. 과거 기숙사에 살 때 식수 인원이 제대로 예측되지 않아서 식사의 질도 떨어지고, 어떤 날은 늦게 가면 밥이 없기도 했던 기억이 있습니다. 이러한 불편함을 해소하는 알고리즘이라니, 굉장히 매력적이었고 도메인이 사람의 심리이니만큼 자신도 있어 참가하게 되었습니다. 

 최종 순위 33등(상위 7%)으로 수상은 하지 못했지만 훌륭한 팀원분들 덕분에 많이 배우고 즐거웠던 대회였습니다. 대회에 처음 참여하거나 작업 진행에 있어 어려움을 겪으시는 분들에게 **작은 등불**이 되길 바라며 이 글을 작성해봅니다. [전체 Code](https://dacon.io/competitions/official/235743/codeshare/2974?page=1&dtype=recent) / [인사이트 Code](https://colab.research.google.com/drive/1Or9Ok3Cx-yth_dQLKmb2Kd82uIhXHPaU#scrollTo=Xbkeup5j0qui)

---

## 💻 작업 방향

 저는 이번에 운이 좋게 다른 대회에서도 종종 뵙던 datu님, 이산님, willow8님과 팀을 이뤄 작업하였습니다. 팀은 [데이콘 팀 모집 오픈카톡방](https://open.kakao.com/o/g2XvVilc)을 통해서 결성되었습니다. 

* 데이터 탐색(EDA) → 아이디어 회의 → 관련 논문 리뷰 → 파생변수 생성 → 외부 데이터 추가 → 모델 선정 및 파라미터 튜닝

 전체적인 작업은 위의 과정을 거쳤고 매주 일요일마다 ZOOM을 통해 서로의 작업 내용 보고 및 아이디어 회의를 진행했습니다. 두 명은 메뉴 작업하고, 다른 두 명은 모델 개발 및 변수 선택하는 등 일주일마다 역할을 나눠서 작업하였고 협업 툴로는 Google Drive와 Google Sheet, Colab을 사용했습니다. 아래 그림과 같이 점수와 수정 내용 등을 기록하며 협업한 게 큰 도움이 되었습니다.

![image-20210728104340962](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728104340962.png)

### 📑 참고 논문

[머신러닝을 이용한 단체급식 다중코너 식수 예측 모델 연구(2021)](http://www.riss.kr/search/detail/DetailView.do?p_mat_type=be54d9b8bc7cdb09&control_no=b50f3c1b6f3af86cffe0bdc3ef48d419)

[기계학습방법을 활용한 대형 집단급식소의 식수 예측(2019)](http://koreascience.or.kr/article/JAKO201912261946958.pdf)

[한식 분야의 듀이십진분류법 수정 전개 방안에 관한 연구(2009)](https://www.koreascience.or.kr/article/JAKO201117463456708.pdf)

### 🌏 외부 데이터

 저희는 공휴일 전후 여부, 날씨, 메뉴를 통해 식수 인원과의 연관성을 찾고자 하였습니다. 관측 데이터는 data leakage에 해당하기 때문에 예보 데이터를 사용했습니다. 이번 대회가 특히 data leakage 요소가 많았는데, 다들 유의하시길 바랍니다.

* 휴일(공휴일/대체공휴일) 데이터 [link](https://www.data.go.kr/data/15012690/openapi.do)

* 날씨 데이터 [link](https://www.data.go.kr/tcs/dss/selectApiDataDetailView.do?publicDataPk=15057682) (진주시 충무공동 데이터) - 불쾌지수와 강수형태를 이용하여 명목변수로 사용

  * LH 본사 위치 : 경상남도 진주시 문산읍 충의로 19

  * 강수형태 : 없음(0), 비(1), 비/눈(2), 눈(3), 소나기(4), 빗방울(5), 빗방울/눈날림(6), 눈날림(7) → 비/눈은 비와 눈이 섞여 오는 것을 의미 (진눈개비) [repo](http://www.riss.kr/search/detail/DetailView.do?p_mat_type=be54d9b8bc7cdb09&control_no=b50f3c1b6f3af86cffe0bdc3ef48d419)

    ![image](https://user-images.githubusercontent.com/54921730/122006828-4480bf00-cdf2-11eb-8810-72a9b1e42a1e.png)

  * 불쾌지수 = 1.8x기온 - 0.55x(1-습도)x(1.8x기온-26)+32 [ref](https://www.kma.go.kr/HELP/basic/help_01_05.jsp)

    ![image](https://user-images.githubusercontent.com/54921730/122006864-506c8100-cdf2-11eb-9da5-3c4d88b10df3.png)

* 메뉴 분류 [link](https://namu.wiki/w/%EC%87%A0%EA%B3%A0%EA%B8%B0) - 나무 위키의 기준을 참고하여 분류

---

## 👀 EDA

구내식당의 요일별 점심, 저녁식사를 먹는 인원을 예측 → 점심, 저녁 다른 모델을 사용할 것
train : 2016-02-01 ~ 2021-01-26 / test : 2021-01-27 ~ 2021-04-19

### Data Info

* 일자, 요일, 본사정원수, 본사휴가자수, 본사출장자수, 본사시간외근무명령서승인건수, 현본사소속재택근무자수, 조식메뉴, 중식메뉴, 석식메뉴, 중식계, 석식계로 이루어져 있음

### 결측치

* NULL 값은 존재하지 않았지만, 석식이 제공되지 않는 날이 있었음
  * 매달 마지막 주 수요일에 자기계발의날 또는 가정의날로 석식이 제공되지 않거나, 드물게 긴 연휴의 전날 등의 특성을 띔
  * 중식은 정상적으로 제공되었기에 석식 모델에서만 제거

![image-20210728125247852](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125247852.png)

### 년, 월, 일, 주차, 계절

각 컬럼별로 유의미한 차이가 있는 것으로 식별됨

![image-20210728125314604](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125314604.png)

![image](https://user-images.githubusercontent.com/54921730/122222758-85113300-ceed-11eb-8d34-b9443e727ead.png)

![image](https://user-images.githubusercontent.com/54921730/122225966-7c6e2c00-cef0-11eb-8a7a-fc7593b6f113.png)

### 재택근무자

재택근무자가 처음 발생한 2020-01-06 이전과 이후의 변수들의 상관관계가 크게 달라짐

* is_corona 컬럼 추가

![image-20210728125338814](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125338814.png)

![image](https://user-images.githubusercontent.com/54921730/122203721-4a050480-ced9-11eb-8204-256b699d1390.png)

* 하지만 굉장히 의외였던 건 재택근무자의 수가 늘었다고 식수 인원이 줄어들지는 않은 점이었습니다
  * 이 대회에서 가장 충격받은 인사이트였는데, 재택근무자가 늘었다는 것은 코로나의 확산세가 강했다는 것이기에 사외에서 식사하던 인원이 구내식당을 사용하게 되면서 그런 현상이 발생한 게 아닐까 추측했습니다.
  * 이렇게 제가 당연하다고 생각했던 것들을 반박해주고, 다시금 생각하게 하는 것이 데이터 분석의 매력이 아닐까 싶습니다.

![image-20210728125359506](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125359506.png)![image-20210728141039234](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728141039234.png)

### 요일

요일별로 큰 차이를 보인다.

* 월, 금의 경우 휴가자가 많음
* 수, 금의 경우 야근자가 적음
* 월요일의 경우 중식을 상당히 많이 먹음.

![image](https://user-images.githubusercontent.com/54921730/122228864-3bc3e200-cef3-11eb-9af9-08398b8a9867.png)

![image-20210728125555412](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125555412.png)

![image-20210728122030048](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728122030048.png)

![image-20210728141111029](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728141111029.png)

### 공휴일

* 공휴일 전후 여부와 중식계 석식계 연관성을 발견

![image-20210728125634009](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125634009.png)

![image-20210728121905808](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728121905808.png)

### 이상치

* 금요일의 경우 2019년 1월 4일을 기점으로 야근자의 수가 확 줄어듬. 
  * 내부 정책의 변화로 추측
  * 이것도 하나의 컬럼으로 추가할 수 있을 듯
  * 그러다 [2019년 10월 4일 LH 국정감사](https://www.mk.co.kr/news/realestate/view/2019/10/795387/)로 인해 야근자가 폭증 → **이상치로 처리, 제거**
* 수요일의 경우 초반 4번의 경우만 야근자가 존재하고 그 후로는 거의 0에 수렴
  * 마찬가지로 2019년 10월 2일, 2018년 10월 10일 야근자 폭증 →  **이상치로 처리, 제거**

![img](https://raw.githubusercontent.com/herjh0405/Img/master/img/img.png)

---

## 🎯 모델 선정 및 파라미터 튜닝

* 마지막으로 모델은 Catboost를 추가한 Pycaret과 Flaml을 통해 비교하였습니다.

  - 이외에도 LSTM, KNN Regressor, CNN, Prophet 등 여러 모델을 사용했지만, Xgboost와 LightGBM 모델이 가장 높은 성능을 띈다고 판단해, 두 모델을 ensemble 해주었습니다.
  - 변수 선택은 전진 선택법과 shap value, 수작업 등을 통해 완료했고, Parameter 또한 Optuna 모델과 수작업으로 적절한 값을 찾았습니다.
  - 자세한 코드는 상단의 코드 링크를 확인 부탁드립니다.

  ![image-20210728125814635](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728125814635.png)


이번에 제출을 DACON API를 활용해서 진행해봤는데 굉장히 편리했습니다.

![image-20210728130006412](https://raw.githubusercontent.com/herjh0405/Img/master/img/image-20210728130006412.png)

---

## 마무리

 전체적으로 이번 대회를 통해 협업과 대회 진행하는 방법에 대해 많이 배웠습니다. 다시 한번 저와 함께 작업해주신 이산님, datu님, willow8님께 감사드리며, 모쪼록 이 글이 조금이나마 도움이 되시길 바라겠습니다. :)