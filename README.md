# 📊 ELK 스택을 이용한 타이타닉 데이터 분석


## 👩‍💻 팀원 소개



|                                         노솔리(🩸AB)                                          |                                      박웅빈(🩸A)                                      |                                        이주원(🩸B)                                        |                                         홍민영(🩸O)                                          |
| :-------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------: |
| <img  width="100px" src="https://avatars.githubusercontent.com/soljjang777" /> | <img width="100px" src="https://avatars.githubusercontent.com/Ungbbi" /> | <img width="100px" src="https://avatars.githubusercontent.com/2oo1s"/> |     <img width="100px" src="https://avatars.githubusercontent.com/u/65701100?v=4"/>     |
|                       [@soljjang777](https://github.com/soljjang777)                        |           [@Ungbbi](https://github.com/Ungbbi)           |                      [@2oo1s](https://github.com/2oo1s)                      |                    [@HongMinYeong](https://github.com/HongMinYeong)                     |



## 🟢 프로젝트 개요

이 프로젝트는 Kaggle의 타이타닉 데이터를 Ubuntu 서버에서 ELK(Elasticsearch, Logstash, Kibana) 스택을 설정하여 분석하는 과정을 보여줍니다. 
타이타닉 데이터는 MySQL에 저장되고, Elasticsearch를 통해 불러온 뒤, Kibana를 사용하여 시각화합니다.

[데이터 출처] : https://www.kaggle.com/competitions/titanic/data?select=train.csv <br />

## ⚙️ 설정

1. **MySQL JDBC 드라이버 다운로드:**
   ```bash
   # 다운로드
   wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.18.zip
   # 다운로드한 파일 압축 해제:
   tar -xvzf mysql-connector-java-8.0.18.tar.gz
   # JDBC 드라이버 JAR 파일을 Logstash의 디렉토리에 복사
   sudo cp mysql-connector-java-8.0.18/mysql-connector-java-8.0.33.jar /usr/share/logstash/logstash-core/lib/jars/

2. **Logstash 설정 파일 작성:**
   - /etc/logstash/conf.d/ 경로에 생성
   ```conf
   input {
    jdbc {
     jdbc_driver_library => "/usr/share/logstash/logstash-core/lib/jars/mysql-connector-java-8.0.18.jar"
     jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
     jdbc_connection_string => "jdbc:mysql://localhost:3306/your_database"
     jdbc_user => "your_username"
     jdbc_password => "your_password"
     statement => "select * from titanic_raw"
    }
   }

   output {

   # 콘솔창에 어떤 데이터들로 필터링 되었는지 확인
   stdout {
     codec => rubydebug
   }

    # 위에서 설치한 Elasticsearch 로 "titanic" 라는 이름으로 인덱싱 
    elasticsearch {
      hosts => ["http://localhost:9200"]
      index => "titanic"
    }
   }
 4. **elasticsearch & logstash 재가동:**
    ```bash
    # 재가동
    sudo systemctl restart elasticsearch
    sudo systemctl restart logstash
    # 가동 확인
    sudo systemctl status elasticsearch
    sudo systemctl status logstash

## 🧊 ICE BREAKING
   <img src="https://github.com/user-attachments/assets/7cb30461-1c7f-4acd-b572-9c72c5fa87aa" />


### 침몰 연도
타이타닉호는 **1912년**에 침몰했습니다.

### 산업혁명과 사회적 변화
- **산업혁명**으로 인해 많은 사람들이 농촌에서 도시로 이동하였고, 대규모 공장과 산업지대가 발전했습니다.
- 이 과정에서 많은 사람들이 새로운 기회를 찾아 해외로 이주하거나 더 나은 삶을 찾기 위해 대서양을 건너는 경우가 많았습니다.
- 타이타닉호의 3등급 승객 대부분이 이민자들이었던 것은 이러한 사회적 배경과 관련이 있습니다.

### 교통수단의 발전
- 산업혁명은 **교통수단의 발전**을 촉진했습니다. 기차와 증기선의 발전으로 더 많은 사람들이 여행할 수 있게 되었습니다.
- 이는 다양한 계층의 사람들이 장거리 여행을 떠나게 만든 원동력이 되었습니다.

### 타이타닉호의 객실
- 타이타닉호는 당시의 최신 기술로 만들어진 **고급 여객선**이었습니다.
- 1등급과 2등급 객실은 상당히 비쌌으나, 3등급 객실은 상대적으로 저렴한 가격으로 제공되었습니다.
- 대서양 횡단 항로를 제공하며 많은 사람들을 수송할 수 있는 대형 선박이었고, 이로 인해 3등급 객실에 많은 승객을 수용할 수 있었습니다.



## 📝 타이타닉 데이터 분석
1. 동반 인원 수별 등급 비율: 3등급에서 동반 인원 수가 높음.
2. 등급별 생존 비율: 3등급에서 사망비율이 가장 높다는 것을 알 수 있음.
3. 클래스별 성별 평균 생존 비율: 각 클래스별로 남성의 사망율이 높음. 
4. 클래스별 연령 분포: 전클래스에서 30-40대 비율높음. 어느정도 경제여력도 있고 여행을 가장 많이 즐기는 나이대로 보임.


## 📊 분석 데이터 시각화 
<details>
<summary> 📚 테이블 구조 </summary>
<div markdown="1">

<img src="https://github.com/user-attachments/assets/203f41db-b3e0-4184-b905-b015c913febc" />

</div>
</details>

1. **항구별 클래스 인원 비율** <br />
   <img src="https://github.com/user-attachments/assets/bc1b4e47-3149-4d96-8852-206868c1a106" />

   > **영국 항구와 타이타닉호 3등급 승객 분포**
   >
   > 다음 그래프를 보면 영국 항구가 3등급 승객이 가장 많이 분포한 것을 확인할 수 있습니다. 이는 타이타닉호가 출   항한 1912년도 시대적 배경에 맞춰 영국의 시대적 상황과 밀접한 연관이 있습니다.
   >
   > **영국의 시대적 상황 (1912년)**:
   > - **영국의 산업혁명**: 타이타닉 사건이 일어났던 해는 1912년으로 영국의 후기 산업혁명시기(1870년대 - 1900년대) 입니다. 오랜 산업혁명으로 노동 환경의 변화와 경제 구조 변화로 인해 여가 시간과 경제적 여유가 생겨서 승선 승객이 많은걸 알 수 있었습니다. 
   > - **타이타닉호의 출항**: 타이타닉호는 영국의 주요 항구에서 출항했으며, 고급 여객선으로서 1등급과 2등급 객실은 비쌌지만, 3등급 객실은 상대적으로 저렴했습니다. 많은 이민자들이 경제적 이유로 3등급 객실을 선택했고, 이로 인해 영국 항구에서 3등급 승객이 많이 분포하게 되었습니다.

> 영국의 산업노동자 승객 타겟으로한 여행상품을 더 많이 만들 수 있습니다. 

2. **성별에 따른 생존율 & 연령대별 생존율** <br />
<table>
  <tr>
    <td> <img src="https://github.com/user-attachments/assets/b5c7f42d-43f3-4387-b9b8-4f4adaa3d69f" />
    </td>
    <td> <img src="https://github.com/user-attachments/assets/2080e185-ac0b-43b7-acf6-83e78d9fe8b2"/></td>
  </tr>
</table>

3. **등급별 생존 비율** <br />
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/5564b43c-73ff-4699-a5f9-afffb0976ce9" alt="등급별 생존 비율 1" width="450"></td>
    <td><img src="https://github.com/user-attachments/assets/3df09c67-bc45-47ec-9f1f-0eef204b35bb" alt="등급별 생존 비율 2" width="450"></td>
  </tr>
</table>

 > 2와 3의 분석과 관련된 연구 논문
      "https://www.pnas.org/doi/abs/10.1073/pnas.0911303107" <br />
      - 타이타닉호의 생존자들 중 여성과 어린이 그리고 상대적으로 가격이 높은 등급 객실의 사람들의 생존율이 높은 원인에 대해서 연구 <br />
      - 느리게 침몰하는 타이타닉호 (2시간 40분)에서는 사회적으로 결정된 행동 패턴(사회적 규범)이 다시 나타날 수 있다.<br />
      - 반면 루시타니아호 (18분)에서는 단기 비행 충동으로 인해 행동이 지배되어 사회적 규범이 의미가 없어졌다.<br />

 - 즉. 타이타닉에서 여성, 어린이, 경제적지위가 높은 자들이 생존할 수 있었던 것은 사회적 규범이 작용했기 때문이라고 볼 수 있는데, <br />
    이 규범이 작용하는 데에는 적은 시간에서는 단기 비행 충동으로 인해 작용하기 어렵다.

4. **동반 자식 유무별 생존율** <br />
   <img src="https://github.com/user-attachments/assets/9f40e44b-07a9-4e78-bd0e-2756f3f07925" />
   

5. **클래스별 성별 평균 생존 비율** <br />
   <img src="https://github.com/user-attachments/assets/d70490c3-6786-460d-9e87-a21c36bb0a01" />
6. **클래스3 남성의 연령대별 생존율** <br />
   <img src="https://github.com/user-attachments/assets/d7b7e25e-26d2-472b-a392-7be265c8b2a3">
  
## ➕ 제안
 1. 3등급 승객의 사망 비율이 높음.
 2. 3등급 승객 중 남성의 사망 비율이 특히 높음: 3등급 승객 중 남성의 사망 비율이 특히 높다는 결과를 확인했습니다.
    
  **❕ 대상 고객층** <br />
  동반 자식 혹은 동반 부모 없이 홀로 여행하는 2-30대 남성의 보험료는 높게 측정하는 것이 어떨까?  <br />
  **❕ 보험 상품 제안** <br /> 
  가족의 가장이 사망할 경우, 특히 3등급 승객과 같은 상황을 고려하여 적절한 보장과 지원을 제공하는 보험 상품을 개발하는 것을 제안 드립니다.  <br />
  자식 또는 부모 동반 여행 시 가족으로 결합하는 여행자 보험 상품은 어떨까 ?

