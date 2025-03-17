# ses
- SES를 사용하면 이메일을 보낼 수 있습니다
- IAM은 SES와 완전히 통합될 수 있습니다
- SES 하면 이메일만 기억하세요 즉 시험 문제가 이메일에 관한 거라면 SES가 정답일 수 있지만 그 외에는 해당 사항 없습니다

# amazon opensearch
- 여러분은 인덱스나 기본키를 이용해서만 데이터베이스의 데이터를 쿼리할 수 있어요
- 분석적 쿼리도 할 수 있어요
- 또는 서버리스 경로를 택해서 서버리스 클러스터를 가질 수도 있어요 그러면 스케일링부터 운영까지 모든 걸 AWS에서 처리하죠
- 예를 들어 Kinesis Data Firehose나 IoT, CloudWatch Logs나 어떤 커스텀 앱으로부터도 데이터를 받을 수 있어요 Cognito, IAM 통합을 통해 보안을 확보할 수 있고요

- ![image](https://github.com/user-attachments/assets/2430bfca-b393-43a9-916e-59bc282ca8ef)

- ![image](https://github.com/user-attachments/assets/08c0a8ac-2898-4ff9-b38b-802d645457c9)


- ![image](https://github.com/user-attachments/assets/ab62615c-b0e3-48f3-a36f-84fc7dea443a)

# amazon athena
- Athena는 Amazon S3 버킷에 저장된 데이터의 분석을 도와주는 서버리스 쿼리 서비스입니다
- 데이터를 분석하려면 표준 SQL 언어를 사용해 파일을 쿼리해야 합니다, Athena의 아래 단은 Presto 엔진으로 구축되어 있고, 여기서 SQL 언어를 사용합니다
- Athena는 보통 Amazon QuickSight라는 도구와 함께 사용해 보고서 및 대시보드를 생성합니다
- 사용하고, AWS 서비스에서 생성된 모든 종류의 로그를 쿼리하고 분석하는 데 사용합니다 VPC 흐름 로그나 로드 밸런서 로그, CloudTrail 추적 등을 분석할 수 있죠
- 따라서 Amazon Athena에 적합한 데이터 포맷은 Apache Parquet과 ORC입니다 이 포맷을 사용하면 성능을 크게 개선할 수 있어요
- Glue 같은 서비스를요, Glue를 사용하면 데이터를 손쉽게 ETL 작업으로 변환할 수 있습니다,

# amazon msk 
- 관리형 스트리밍
- Kafka는 Amazon Kinesis와 같은 기능을 합니다
- Kafka와 Kinesis 모두 데이터를 스트리밍할 수 있죠
- MSK를 사용하면 AWS에서 완전관리형 Kafka 클러스터를 구성할 수 있고, 클러스터를 그 자리에서 생성하고 업데이트 및 삭제 가능
- 일반적인 Kafka 실패로부터 자동으로 복구할 수 있으며, 데이터는 원하는 기간만큼 EBS 볼륨에 저장할 수 있습니다
- Kinesis Data Streams은 메시지 크기가 1MB로 제한되어 있습니다, Amazon MSK의 기본 설정값과 같죠, 하지만 MSK에서는 메시지 크기를 키울 수 있습니다, 10MB로 늘릴 수도 있죠
- Kinesis Data Streams는 전송 중 암호화를 사용하고, MSK는 플레인텍스트 또는 전송 중 암호화를 사용할 수 있습니다
- Amazon MSK에서는 데이터를 원하는 기간만큼 저장해둘 수 있습니다
  - 1년 이상 저장할 수도 있죠, EBS 스토리지 비용만 계속해서 지불한다면 상관없습니다

- ![image](https://github.com/user-attachments/assets/dfff8531-8664-490d-9744-36bf17410346)

# acm
- ACM이 도메인으로 연결되게 되면 이러한 TLS 인증서의 프로비저닝과 유지 관리를 허용해 줍니다
- ACM은 공용 및 사설 TLS 인증서를 모두 지원하고 공용 TLS 인증서에 대한 사용은 무료입니다
- 일래스틱 로드 밸런서나 CloudFront Distributions API Gateway의 API 등이 모두 가능하죠
- 사설 인증서를 발급하려면 AWS Private CA를 구성해야 합니다
- 이는 관리형 서비스로, 이를 사용해 루트 인증 기관 또는 하위 인증 기관을 생성할 수 있습니다
  - 여기서 하위 인증 기관(CA)이란 루트에 종속된 인증 기관을 뜻합니다

  - ![image](https://github.com/user-attachments/assets/cbf30851-0d30-47b7-98be-2bca809ac106)

# aws macie
- Macie는 머신러닝과 패턴 매칭을 이용해서 AWS에 있는 여러분의 민감한 정보를 발견하고 보호하죠
- Macie는 그걸 분석하고요, Macie는 어떤 데이터를 PII로 분류할 수 있는지 확인할 거예요
- ![image](https://github.com/user-attachments/assets/2f21d029-8072-4bb8-822b-c03009755448)

# aws appconfig
- 앱 구성 정보를 앱과 함께 배포하는 겁니다
- 그런데 구성 데이터를 앱 외부에 두고 싶다면 어떻게 해야 할까요? 이럴 때 AppConfig를 사용합니다
- 동적으로 배포할 수 있습니다, 구성 데이터를 변경해도 애플리케이션은 이와 상관없이 애플리케이션에 새로운 코드가 배포되었을 때 변경된 구성을 반영합니다
- 거의 모든 구성 정보를 동적으로 변경할 수 있습니다, 따라서 애플리케이션 성능을 미세하게 조정하는 것도 가능하고 심지어 차단할 IP 목록이나 허용할 IP 목록을 AppConfig에서 변경해 실시간으로 반영하는 것도 가능합니다,
- 파라미터 저장소가 될 수도 있고, SSM 문서나 S3 버킷 등의 장소를 지정할 수 있죠

- ![image](https://github.com/user-attachments/assets/1a1e574d-231a-4e9c-aa61-3ae2414013b3)

# cloudwatch evidently
- 이건 여러분이 앱의 새로운 피처를 테스트할 수 있게 해주는 CloudWacth의 기능이고요
- 새로운 피처의 성능을 모니터링해서 그 피처를 테스트하려 할 수도 있어요
- 첫째는 Launches예요, 일반적으로는 피처 플래그라고 부르고요 이 경우엔 일부 사용자에게 피처를 활성화하거나 비활성화할 수 있어요
- 그들은 우리가 CloudWatch Evidently에서 설정한 것을 보게 되죠 이런 식으로 해서 여러분은 새로운 피처의 런치를 테스트하거나 A/B 테스팅 실험을 할 수 있어요
- 그리고 베타 테스터는 우리가 오버라이드하도록 설정한 걸 모두 확인할 수 있겠죠 이 부분은 시험에 나올 수 있어요
