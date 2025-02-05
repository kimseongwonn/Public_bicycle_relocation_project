# Public_bicycle_relocation_project
(공공자전거 따릉지 쏠림현상 재배치 - 24.04.26~24.05.09)
본 프로젝트는 멀티캠퍼스 데이터 분석 국비교육 세미프로젝트 활동으로 2024.04.26 ~ 2024.05.09 기간 내 진행한 프로젝트입니다.

주제는 **'서울시 공공자전거 비용 절감을 위한 재배치 최적화 방법 제안'**으로 선정하였습니다.

여기서 공공자전거란, 서울시에서 운영하는 ‘따릉이’를 의미합니다.

## **✔️ 프로젝트 배경**

1. 친환경 이동 수단으로 각광받는 따릉이, 누적 이용 건수 1억 4000건 돌파
2. 2017년부터 2019년까지 서울시 우수 정책 1위로 꼽힐 정도로 긍정적인 시민 반응
3. 그러나 연간 적자 100억 넘어서며 공공자전거 대여 산업의 고질적인 문제점 발생
4. 수익성 개선 방안을 설립하여 사업 지속성 확보 필요성 대두
5. 따라서 따릉이 이용 관련 데이터 탐색을 통한 수익성 개선점 도출을 주제로 선정

## **✔️ 프로젝트 구현 방법**

### **1. 데이터 수집**

- 따릉이 이용 데이터 : 서울특별시_따릉이 대여소별 대여 반납 승객수 정보(서울특별시)
- 따릉이 대여소 정보 : 서울특별시_따릉이대여소 마스터 정보(서울특별시)
- 기상청 데이터 : 기온, 강수량, 미세먼지 데이터(기상청 기상자료개방포털)
- 지역별 용도구분 데이터: 토지 이음 사이트 크롤링(토지 이음 - 이음지도)

### **2. 공공자전거 쏠림현상 정의**

- 대여량과 반납량의 차이를 통해 정의
    - 대여량 = 반납량(평형 상태)
    - 대여량 > 반납량(비평형 상태 - 부족)
    - 대여량 < 반납량(비평형 상태 - 과잉)
    - 이를 토대로 평일 출퇴근 시간대 부족 대여소, 과잉 대여소 정의

**1️⃣ 기상데이터 상관관계 분석**

- 독립변수 : 일 평균 기온, 일 강수량, 미세먼지 정도
- 종속변수 : 일별 이용량
- 따릉이 이용에 있어 기상이 큰 영향을 미칠 것이라 가정한 뒤 기상데이터와 상관관계 분석
- 상관관계가 높고 특이 지점에서 이용량이 감소하는 지점을 따릉이 재배치 일지 중단 지점으로 지정하기 위해 데이터 분석

**2️⃣ 시계열 분석**

- 따릉이 이용량의 시계열성 존재 가정
- 계절에 따라 특성이 뚜렷한 대한민국의 특성에 근거하여 데이터의 계절성 확인
- 일반 ARIMA 분석에서 계절성 패턴을 추가한 SARIMA 모형 선정
- 최적의 SARIMA 모델 파라미터(p,d,q)(P,D,Q,M) 선정을 위한 auto arima 사용

### **3. 쏠림현상 상위 대여소 선정 과정**

- 프로젝트 기간을 고려하여 쏠림 현상이 심각한 지역과 시간대 선정하여 모델 수립 후 다른 지역에 수정 확장하는 것을 목표로 설정

**1️⃣ 서울시 구별 이용량 확인**

- 따릉이 대여소 마스터 정보를 이용하여 대여소 ID 별 지역 전처리
- 대여소 ID를 사용하여 일별 이용량 데이터 지역별 그룹화
- 지역별 이용량 합산 후 시각화
- 강서구 이용량이 가장 많은 것 확인하여 강서구로 지역 선정

**2️⃣ 강서 데이터 시간대별 이용량 확인**

- 기준시간대를 이용하여 1시간 단위 이용량 데이터 추출 및 시각화
- 출퇴근 시간대(06~10시, 17시~21시)의 이용량이 가장 많은 것 확인
- 출퇴근 시간대 전 재배치를 목표로 출퇴근 시간대 대여소 별 수요량 예측을 목표로 설정

**3️⃣ 대여소 위치별 용지 용도 설정**

- 주거 지역 및 상업 지역 선정
    - 주거지역 = 출근 시간 대여량 > 반납량 & 퇴근 시간 대여량 < 반납량
    - 상업지역 = 출근 시간 대여량 < 반납량 & 퇴근 시간 대여량 > 반납량
- 출근 시간에는 주거지역의 수요가 증가(출근을 위한 수요)할 것이라 가정
- 퇴근 시간에는 상업지역의 수요가 증가(퇴근을 위한 수요)할 것이라 가정

**4️⃣ 출퇴근 시간대 쏠림 정도 상위 대여소 확인**

- 앞서 정의한 공공자전거 쏠림 현상에 의거 강서구 내 대여소의 출퇴근 시간대 반납량과 대여량 파악 후 쏠림 정도 파악
- 쏠림정도 : 출퇴근 시간대별 반납량과 대여량 차이의 절대값
- 쏠림정도를 기준으로 정렬 후 그래프 시각화
- 시각화 그래프의 각 지점의 미분값이 급변하는 지점을 기준으로 쏠림 정도 상위 대여소 20곳 선정
  
  *** 더욱 자세한 내용은 “공공자전거_프로젝트_과정 및 결과.pdf” 파일 참고 ***
