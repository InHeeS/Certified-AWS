![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/eed36475-cac3-4735-a15f-6b62588670b1)# Athena

s3 버킷에 저장된 데이터 분석에 사용하는 서버리스 쿼리 서브스로 데이터를 분석하려면 표준 sql 언어로 파일을 쿼리해야 합니다. 

전체 서비스가 서버리스여서 데이터베이스를 프로비저닝 할 필요가 없습니다. 

quicksight 는 s3버키셍 연결된 athena 다음에 배치됩니다. 

사용 사례로는 임시 쿼리 수행이나 비즈니스 인텔리전스 분석 및 보고가 있고 AWS 서비스에서 발생하는 모든 로그를 쿼리하고 분석할 수 있습니다 VPC 흐름 로그, 로드 밸런서 로그 CloudTrail 추적 등이 해당됩니다

*서버리스 sql 엔진을 사용한 s3 데이터 분석이 나오면 Athena 입니다.*

*Athena 성능도 향상이 가능하며 방법으로는 스캔된 데이터당 TB 당 가격을 지불하므로 데이터를 적게 스캔할 유형의 데이터를 사용합니다. 필요한 열만 스캔하므로 비용을 절감합니다.*

권장하는 형식은 apache parquet 와 orc 입니다. 

파일을 Apache Parquet나 ORC 형식으로 가져오려면 Glue 적재 작업이 있습니다. csv 나 parquet간의 데이터를 변환하는 데 매우 유용합니다. 더 적은 데이터를 스캔해야 하므로 데이터를 압축합니다. 

s3 의 정확히 어떤 폴더에서 데이터를 가져와야 하는지 알 수 있어 데이터의 서브셋만 복구하면 됩니다. 

Amazon S3에 작은 파일이 너무 많으면
Athena는 큰 파일이 있을 때보다 성능이 떨어집니다

Athena는 S3뿐만 아니라 어떤 곳의 데이터도 쿼리할 수 있습니다


AWS나 온프레미스에서 어떻게 사용할까요?
데이터 원본 커넥터를 사용하면 됩니다
데이터 원본 커넥터는 Lambda 함수로
다른 서비스에서 연합 쿼리를 실행합니다
CloutWacth Logs, DyanamoDB RDS 등에서 실행하며 매우 강력합니다

# RedShift

postgreSQL 기술에 기반되어있습며 postgresql 과 달리 온라인 거래 처리에는 사용 되지 않습니다. 

olap 유형의 데이터베이스인데, 이는 온라인 분석 처리라는 뜻입니다. 그리고 분석과 데이터 웨어하우징에 사용됩니다. 

다른 모든 데이터 웨어하우스보다 10배 좋은성능을 가지며 pb 의 데이터로 확대 가능합니다. 

성능 개선도 훌륭한데 이는 데이터를 열 기반 스토리지로 사용합니다. 

redshift 클러스터에 공급한 인스턴스만큼 비용을 지불하며 쿼리를 수행하기위해 sql 문장을 곧바로 사용합니다. 

redshift는 s3로부터 모든 데이터를 로드하면 redshift는 Athena 보다 훨씬 바를 것입니다. 바른 조인과 통합을 합니다. 

집중적인 데이터 웨어하우징의 경우 redshift가 더 나은 후보입니다. 

멀티 az 가 있기에 피해보구가 잘도비니다. 

싱글 az 라면 스냅샷을 사용합니다. 

redshift 가 스냅샷을 자동적으로 복사하도록 구성할 수 있습니다. 해당 클러스터에서 다른 aws 리전으로 말입니다. 

redshift로 데이터를 수집하는 방법이 있습니다. 

1. kinesis data firehose를 사용하는 방법입니다.

2. 데이터를 s3로 로드한후 redshift에서 바로 복사 명령이 내립니다.

3. jdbc 드라이버를 사용해서 redshift 클러스터로 데이터를 유입하고 싶다면 그렇게 해도 됩니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/fe6b92d8-76d7-4e2d-8355-9f234ea90149)


redshift spectrum 을 이용하면 쿼리를 시작할 수 있는 redshift 클러스터가 이미 있어야 하고 s3에 데이터가 있고 redshift를 이용해서 분석하고 싶지만 로드하고 싶지않을 때 사용합니다. 

# amazon opensearch

elasticSearch 후속작입니다. 

dynamodB에서 여러분은 데이터베이스의 기본 키나 인덱스만을 이용해서 쿼리하지만

opensearch 를 사용하면, 이름에서 알 수 있듯이 모든 필드에서 검색합니다. 부분 매칭도 가능합니다. 

opensearch 클러스터를 프로비저닝하는 두가지 모드가 있습니다.

1. 관리형 클러스터 모드인데 그 경우에 실제 물리적인 인스턴스가 프로비저닝되고 볼 수 있습니다.

2. 서버리스 경로를 선택해서 서버리스 클러스터를 가질 수도 있습니다. 그 경우에 스케일링부터 운영까지 모두 aws에서 관리하게 됩니다.

oepnsearch 에는 자체 쿼리 언어가 있습니다. sql을 지원하지 않지만 플러그인을 통해서 sql 호환성을 활성화 합니다. 

보안은 cognito, iam 등을 통해 제공되고 주소 암호화와 전송 중 암호화를 사용합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/fe1e9d01-baa5-47dc-afdf-1eb6ed7466f2)


opensearch는 검색능력을 제공하고 데이터의 기본 출처는 dynamoDB 테이블입니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/bb27ab7f-299d-4dd2-a955-dddc6aae6d19)


![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/1ac8ad5d-1eaa-489d-8bbe-f653980794f6)

# EMR

AWS 에서 빅 데이터 작업을 위한 하둡 크럴스터 생성에 사용됩니다. 

방대의 양의 데이터를 분석하고 처리합니다. 

하둡 클러스터는 프로비저닝해야 하며 수백 개의 ec2 인스턴스로 구성 될 수있습니다. 

emr 은 ec2 인스턴스의 클러스터로 구성되며 여러 노드 유형이 있습니다. 

스팟 인스턴스는 태스크 노드에 활용하는게 좋습니다. 종료 될수 있기 때문입니다. 

emr 에서 배포할 때는 장기 실행 클러스터에서 예약 인스턴스를 사용하거나 임시 클러스터를 사용해 특정 작업ㅇ르 수행하고 분석 완료 후에 삭제 가능합니다.

# QuickSight

quickSight 는 서버리스 머신러닝 기반 비즈니스 인텔리전스 서비습니다. 

대화형 대시보드를 생성합니다. 시각화 하며 오토 스케일링이 가능합니다. 

사용 사례에는 비즈니스 분석 시각화 규현 시각화된 정보를 통한 임시 분석 수행 데이터를 활용한 비즈니스 인사이트 획득이 있습니다. 

spice 엔진이며 인 메모리 연산 엔진이며 quickSight의 엔터프라이즈 에디션에는 액세스 권한이 없는 사용자에게 일부 열이 표시되지 않도록 열 수준 보안을 설장합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/c9cfa1cc-1115-4991-ac07-40d1839814ee)


*시험에는 quickSight를 Athena 나 Redshift 와 함께 사용하는 문제가 나옵니다.*

quickSight 의 사용자와 그룹은 서비스 전용입니다. 

iam 사용자와는 다르고 일기 전용 스냅샷이며 분석 결과를 공유합니다. 

액세스 권한이 있는 사용자는 기본 데이터를 볼 수도 있습니다.

# Glue

추출과 변환 로드 서비스를 관리하며 etl 서비스라고도 합니다. 

분석을 위해 데이터를 준비하고 변환하는데 매우 유용합니다. 

완전 서비리스 서비스이며 열을 추가하는등 원하는 대로 원하는 대로 데이터를 변형할 수 있습니다. 그럼 다음 최종 출력 데이터를 redshift 데이털 웨어하우스에 로드합니다. 

간단한 코드를 작성하고 etl 작업ㅇ르 시작하면 됩니다. 

*다른 예시로는 데이터를 parquet 형식으로 변환하는 것입니다.*

ahtena 같은 서비스와 함께 사용하면 효과적입니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/7bce2290-29c4-4039-b2f5-71cccec0d97d)

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/7189b7f6-5186-4afb-86ab-7e40cbdde692)

glue 의 기능으로는 다음과 같습니다.

*glue 작업 북마크입니다. 새 etl 작업을 실행할 대 이전 데이터의 재처리를 방지합니다*

glue elastic views 는 sql을 사용해 여러 데이터 스토어의 데이터를 결합하고 복제합니다. 

# AWS Lake Formation

데이터 분석을 위해 모든 데이터를 한곳으로 모아 주는 중앙 집중식 저장소입니다. 수개월 씩 걸리는 작업을 며칠 만에 완료 가능합니다. 

카탈로깅, 복제같은 복잡한  수작업을 자동화하고 기계학습(ml) 변환 기능으로 중복제거를 수행합니다. 

lake formation 에 연결된 애플리케이션에서는 세분화된 액세스 제어가 가능합니다. 

*사용하는 이유로는 바로 중앙화된 권한입니다.*

사용자는 허용된 데이터만 볼 수 있어야 하고 읽기 권한이 있어야합니다. 사용자 수준의 보안을 설정하여 s3 버킷 정책이나 사요자 정책에 보안 설정을 합니다. 이러한 여러 보안들을 lake formation은 액세스 제어기능과 열 및 행 수준 보안이 있습니다. 

*Athena, QuickSight 등 어떤 도구를 사용하든
Lake Formation에 연결하면 한곳에서 보안을 관리할 수 있습니다*

# Kinesis Data Analytics

두가지 종류가 있습니다.

1. sql 애플리케이션용

실시간 데이터가 풍성해집니다. s3로 데이터를 강화 가능하며 완전 관리형 서비스이므로 서버를 프로비저닝하지 않습니다. 오토 스케일링이 가능하며 전송된 데이터만큼 비용을 지불합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/c2b05043-555e-491c-9dd6-d641367ec587)

2. apache flink 용

analytics 의 flink 전용 클러스터에서 실행합니다. 백그라운드 에서요 

병렬 연산과 오토 스케일링을 할 수 있습니다. 체크포인트와 스냅샷으로 구현되는 애플리케이션 백업이 가능하고 apache flink 프로그래밍 기능을 사용 가능합니다. 참고로 kinesis data firehose 의 데이터는 읽지 못합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/aeca61a8-9bb6-469c-903a-25f884d05d65)

# Amazon MSK

Kafka는 Amazon Kinesis 의 대안입니다. 

두 서비스 모두 데이터를 스트리밍합니다. 

msk 에서 apache kafka를 실행하지만 서버 프로비저닝이나 용량 관리가 필요 없습니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/a83980db-65da-466a-a55b-4491e595feff)

Kinesis Data Streams와 Amazon MSK의 차이점을 살펴보죠
Kinesis Data Streams에는 1MB의 메시지 크기 제한이 있습니다

Amazon MSK에서는 1MB이 기본값이고
더 큰 메시지 보존을 위해 10MB로 설정할 수도 있습니다

Kinesis Data Streams에선 샤드로 데이터를 스트리밍합니다
Amazon MSK에선 파티션을 이용한 Kafka 주제를 사용하죠

Kinesis Data Streams에는 TLS 전송 중 암호화 기능이 있고
Amazon MSK에는 평문과 TLS 전송 중 암호화 기능이 있습니다

두 클러스터 모두 저장 데이터 암호화가 가능합니다

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/0d227998-361f-4518-809f-3f519bc601c7)

# quiz

Question 4:
Redshift에서 클러스터와 데이터 저장소 사이의 모든 COPY와 UNLOAD 트래픽이 VPC를 통하도록 강제하는 기능은 무엇입니까?

Answer : 향상된 vpc 라우팅 


Question 10:
AWS Glue에서 앞선 Glue ETL 작업 실행 중에 이미 처리된 데이터를 저장하고 추적할 수 있게 해주는 기능은 무엇입니까?

Question 10:
AWS Glue에서 앞선 Glue ETL 작업 실행 중에 이미 처리된 데이터를 저장하고 추적할 수 있게 해주는 기능은 무엇입니까?

Question 10:
AWS Glue에서 앞선 Glue ETL 작업 실행 중에 이미 처리된 데이터를 저장하고 추적할 수 있게 해주는 기능은 무엇입니까?

Answer : glue job bookmarks



