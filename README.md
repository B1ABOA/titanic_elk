# 📊 ELK 스택을 이용한 타이타닉 데이터 분석


## 👩‍💻 팀원 소개



|                                         노솔리(AB)                                          |                                      박웅빈(A)                                      |                                        이주원(B)                                        |                                         홍민영(O)                                          |
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

    # 위에서 설치한 Elasticsearch 로 "bank" 라는 이름으로 인덱싱 
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

## 📝 타이타닉 데이터 분석
1. 동반 인원 수별 등급 비율: 3등급에서 동반 인원 수가 높음.
2. 등급별 생존 비율: 3등급에서 사망비율이 가장 높다는 것을 알 수 있음.
3. 클래스별 성별 평균 생존 비율: 각 클래스별로 남성의 사망율이 높음. 
4. 클래스별 연령 분포: 전클래스에서 30-40대 비율높음. 어느정도 경제여력도 있고 여행을 가장 많이 즐기는 나이대로 보임.


## 📊 분석 데이터 시각화
1. **동반 인원 수별 등급 비율** <br />
   <img src="https://github.com/user-attachments/assets/a18af5c9-157d-446f-9f79-63db0fdec22b" />

2. **등급별 생존 비율** <br />
<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/5564b43c-73ff-4699-a5f9-afffb0976ce9" alt="등급별 생존 비율 1" width="450"></td>
    <td><img src="https://github.com/user-attachments/assets/3df09c67-bc45-47ec-9f1f-0eef204b35bb" alt="등급별 생존 비율 2" width="450"></td>
  </tr>
</table>


3-1. **클래스별 성별 평균 생존 비율** <br />
   <img src="https://github.com/user-attachments/assets/d70490c3-6786-460d-9e87-a21c36bb0a01" />

3-2. **성별 평균 생존 비율** <br />
   <img src="https://github.com/user-attachments/assets/b5c7f42d-43f3-4387-b9b8-4f4adaa3d69f" />

4-1. **클래스별 연령 분포** <br />
   <img src="https://github.com/user-attachments/assets/0c66ad0d-cedb-4e7c-b820-2a984657a6dd" />
   
4-2. **연령대별 생존 비율** <br />
   <img src="https://github.com/user-attachments/assets/2080e185-ac0b-43b7-acf6-83e78d9fe8b2"/>

5. **항구별 클래스 인원 비율** <br />
   <img src="https://github.com/user-attachments/assets/bc1b4e47-3149-4d96-8852-206868c1a106" />

## ➕ 결론 및 제안
 1. 3등급 승객의 사망 비율이 높음: 3등급 승객 중에서 자녀를 동반한 비율이 높으며 이들 중 사망 비율도 높은 것으로 나타났습니다.
 2. 남성의 사망 비율이 특히 높음: 3등급 승객 중 남성의 사망 비율이 특히 높다는 결과를 확인했습니다.
    
  **❕ 대상 고객층** <br />
  30-40대 여성 타겟, 특히 가족의 가장이 사망했을 때, 사망 비율이 높은 상황을 반영하여 보험 상품을 설계하는 것이 유리할 것이라 생각했습니다.  <br />
  **❕ 보험 상품 제안** <br /> 
  가족의 가장이 사망할 경우, 특히 3등급 승객과 같은 상황을 고려하여 적절한 보장과 지원을 제공하는 보험 상품을 개발하는 것을 제안 드립니다.  <br />

