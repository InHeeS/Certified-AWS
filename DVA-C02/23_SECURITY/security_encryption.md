# encryption in flight
- TSL은 SSL의 가장 최근 버전입니다
- 데이터를 암호화하기 위해 TLS 인증서가 사용되는 것입니다
- 오직 대상 서버만이 TLS 해독 메커니즘을 사용해서 패키지를 해독하고
- 이것이 전송 중 암호화입니다 -> https 

- 서버 측 저장 중 암호화에 관련한 내용 모든 암호화와 해독이 서버에서 일어나는 것입니다

- 마지막으로 클라이언트 측 암호화에 대해 말씀드리겠습니다
  - 메커니즘에 봉투 암호화를 사용할 수 있습니다
  - 그 암호화된 객체는 어느 스토리지 서비스에든 안전하게 전송될 수 있습니다 예를 들면 FTP 서버, Amazon S3, EBS 볼륨 등에 말입니다
  - ![image](https://github.com/user-attachments/assets/efdeae6d-4cef-4192-97e9-c0487d676cd0)

# key management service
- KMS 서비스는 기본적으로 암호화 키를 관리해 주기 때문에
- AWS KMS의 장점은 CloudTrail을 통해 키를 사용하기 위해 수행된 모든 API 호출을 검사할 수 있다는 건데
- 물론 KMS는 권한 부여를 위해 IAM과 완전히 통합되어 있으며 KMS로 암호화하면
- 따라서 KMS를 사용하려는 경우 API 호출을 통해 사용할 수도 있고 AWS CLI 또는 SDK를 사용할 수도 있는데요
- 잠시 후에 보게 될 '고객 관리형 키'와 혼동되는 관계로 KMS 키로 변경되었죠
- 대칭 KMS 키를 갖는다는 것은 데이터를 암호화하고 복호화하는 데 오직 하나의 키가 사용된다는 의미죠
- KMS에서 사용 가능한 두 번째 키 유형은 비대칭 키인데 키가 두 개라는 의미로
  - 데이터 암호화에 사용되는 공개 키와 복호화에 사용되는 개인 키가 있습니다
  - 비대칭 유형의 키는 KMS 키에 액세스할 수 없거나 액세스 권한이 없는 사용자가 클라우드 외부에서 암호화를 수행하려는 경우에 사용되며,

- 첫 번째는 AWS 소유 키로 무료 키이며
-  AWS 관리형 키는 무료이며 aws/ 뒤에 서비스명이 붙는 형태라서보면 알 수 있습니다, 예를 들면 aws/rds나 aws/ebs, aws/dynamodb처럼 말이죠 무료 키이고 마음대로 사용할 수 있지만 할당된 서비스 내에서만 사용 가능합니다
- 자체 고객 관리형 키와 사용자 지정 키의 경우 월 1달러의 비용이 들며
  - 고객 관리형 키는 1년마다 키 자동 교체를 활성화해야 하며, 
- KMS 키는 리전별로 범위가 지정됩니다
- 그런 다음 스냅샷을 다른 리전으로 복사하려면 다른 KMS 키로 스냅샷을 재암호화해야 하죠

- S3 버킷 정책과 비슷하지만 키에 대한 KMS 키 정책이 없으면 아무도 해당 키에 액세스할 수 없다는 차이점이 있죠
- 사용자 또는 역할이 해당 키 정책에 액세스할 수 있도록 허용하는 IAM 정책이 있으면 됩니다
- KMS 키를 사용할 권한을 부여할 수 있으므로 KMS 키에 대한 교차 계정 접근을 수행하려는 경우에 특히 유용합니다
  - 스냅샷을 생성해야 합니다, 즉 고객 관리형 키를 생성하는 건데 사용자 지정 키 정책을 추가해야 하기 때문이죠, 그런 다음 KMS 키 정책을 추가하여 교차 계정 접근 권한을 부여합니다
  - 그런 다음 암호화된 스냅샷을 대상 계정과 공유하고 대상 계정에서 스냅샷 복사본을 생성하여 해당 계정의 다른 고객 관리 키로
암호화합니다

# kms encrypt aand decrypt
- ![image](https://github.com/user-attachments/assets/21730933-3057-4dc9-b28d-80fa17c0b497)
- 4KB 이상을 암호화하려면 봉투를 사용해서 암호화하는 방법이 있습니다
- GenerateDataKey API를 사용한다는 것입니다, GenerateDataKeyWithout Plaintext가 아니라요
- ![image](https://github.com/user-attachments/assets/2ee8448d-ea8f-4e6b-8e47-948a6a9a5236)
- KMS를 원래 장점대로 키를 생성하는 데에 쓰고 암호화나 해독은 클라이언트 측에서 하는 겁니다
- 암호화 SDK에 데이터 키 캐싱이라는 기능이 있습니다

# kms request quatas 
- KMS의 요청 할당량을 초과하게 되면 ThrottlingException이 나올 겁니다
- 지수 백오프 전략을 사용합니다
- 이를 어떻게 해결할 수 있을까요? GenerateDataKey API를 사용하고 있다면 DEK 캐싱을 쓰면 됩니다
- 다른 방법은 요청 할당량을 늘리는 겁니다 API 호출이나 AWS 고객센터 문의를 통해

# s3 bucket for sse-kms encryption
- 그러면 Amazon S3가 KMS 암호화에 사용하는 비용 또한 99% 절감을 할 수 있게 되죠
- 고객 마스터 키와 KMS가 Amazon S3 버킷을 위한 데이터 키를 생성하는 데에 사용이 됩니다

# kms key policy
- 키 정책은 KMS 키에 액세스할 수 있는 사람을 정의할 때 사용합니다 적절한 IAM 권한을 가진 사람에 한해서요
- 있는 것처럼요, 어떤 KMS 작업을 허용할 건지도 지정할 수 있죠, 암호화 복호화 또는 그 밖의 작업을요 또한 보안 주체(Principal)를 지정할 수 있습니다
- Principal 요소에 IAM 역할을 추가함으로써 간접적으로 IAM 역할 보안 주체를 지정할 수도 있습니다 IAM 역할 세션도 지정할 수 있어요 위임된 역할이나 Cognito Identity 또는 SAML과 같은

# Cloudhsm
- 그러나 CloudHSM을 사용하면 AWS는 일부 암호화 하드웨어를 프로비저닝합니다
- 그러면 AWS가 아닌 우리가 직접 암호화 키를 완전히 관리하게 됩니다
- CloudHSM 장치는 대칭 및 비대칭 암호화 키를 모두 지원하므로 그 위에 SSL 및 TLS 키 등을 포함할 수 있습니다
- 레드시프트와 CloudHSM은 통합되어 있습니다
- 데이터베이스 암호화 및 키 관리에 CloudHSM을 활용하고자 한다면 CloudHSM은 정말 훌륭한 후보입니다
  - 예를 들어, S3 위에 SCC 유형의 암호화를 구현하려는 경우
- 하지만 CloudHSM 소프트웨어를 사용하면, 키를 관리하고 사용자와 그들의 키 액세스 권한을 관리하게 됩니다 이것이 KMS와는 다른데, KMS에서는 모든 것이 IAM을 통해 관리됩니다
- 이제 CloudHSM 클러스터는 고가용성을 제공하며 여러 AZ에 분산되어 있어서 HA입니다

- ![image](https://github.com/user-attachments/assets/57e7839c-ec2d-4a02-9990-8ae7e9ba8344)

---
- KMS에는 미국 소유의 AWS 관리 및 고객 관리 CMK가 있습니다
- 반면 CloudHSM의 경우 AWS가 HSM 장치에 액세스할 수 없기 때문에 오직 고객 관리형 CMK만 지원합니다
- 키 접근성 측면에서 보면 KMS는 여러 리전에서 액세스할 수 있지만
- CloudHSM은 VPC에 배포되므로 VPC peering를 사용하여 여러 VPC에서 공유할 수 있습니다
- CloudHSM을 사용하면 SSL 및 TLS 가속화를 로드 밸런서 수준에서 사용할 수 있습니다
- 액세스 및 인증의 경우 KMS용 IAM이 있는 반면

- CloudHSM에는 사용자 및 사용 권한 및 키를 관리하는 자체 보안 메커니즘이 있습니다
- CloudHSM에서는 MFA 지원까지 제공합니다
- KMS는 관리형 서비스이고 항상 사용 가능하며
- CloudHSM은 서로 다른 AZ에 걸쳐 다수의 HSM 장치를 보유합니다

# ssm parameter store
- 이는 구성 및 보안 암호를 안전하게 보관하기 위한 저장소입니다
  - 구성 데이터를 암호화해 좀 더 안전하게 보관하고 싶다면 KMS 서비스를 사용할 수도 있습니다,
- 서버리스이며 확장성 및 내구성을 갖고 있고 SDK를 사용하기도 쉽습니다, 또한 구성을 업데이트할 때마다 버전이 생성되어 버전 관리가 가능합니다
- 받을 수 있습니다, CloudFormation을 완벽히 지원하기 때문에 매개변수 저장소에 택의 입력 매개변수로 사용할 수 있습니다

- ![image](https://github.com/user-attachments/assets/217242ba-e5b8-4fdc-9df4-f9ea4092e9a6)
- 여기에 Eventbridge 설정을 추가하면 Eventbridge를 통해 알림을 받을 수 있습니다

# secret manager 
- 신규 서비스고요, 비밀을 저장하기 위한 서비스예요
- 그래서 여러분은 시험에서 비밀이나 RDS 또는 Aurora의 비밀 통합이 나오면 Secrets Manager를 떠올리셔야 해요
- 또 여러분이 아셔야 할 또 다른 피처는 멀티리전 비밀이라는 개념이에요
  - 다수의 AWS 리전에 걸쳐 여러분의 비밀을 복제할 수 있다는 개념인데요
- 재해 복구 전략을 세울 수도 있죠

# ssm vs sm
- Secrets Manager가 더욱 비쌉니다
- 그리고 람다 함수를 이용해서 암호의 순환을 자동화할 수 있죠
- 일부 람다 함수는 예를 들어 RDS, 레드시프트 DocumentDB와 같이 Secrets Manager와 강력하게 통합되어 있는 기능으로부터 제공이 됩니다
- 암호에 KMS 암호화는 필수이며 CloudFormation과의 통합이 가능합니다
- 그리고 Secrets Manager와 밀접히 통합되지 않은 일반적인 암호인 경우에는 직접 람다 함수를 작성해야 합니다

- ![image](https://github.com/user-attachments/assets/1e287a4a-3497-4bf9-9bf2-cb36d7bda9fb)

# cloudformation dynamic references
- 저장된 값들은 cloudformation 템플릿을 참조 가능합니다.
- SSM은 Systems Manager의 Parameter Store에 저장된 plaintext 값을 가져옵니다
- Secrets Manager는 Secrets Manager에 저장된 secret 값을 가져옵니다

- ![image](https://github.com/user-attachments/assets/8824e6dd-e1ae-4db2-8f88-14fbd843e55a)
- RDS 데이터베이스 인스턴스는 동적 참조를 사용하여 Secrets Manager에 저장된 비밀번호를 가져옵니다
  - Dynamic References를 사용하는 것입니다 CloudFormation 템플릿 내에서 비밀번호를 직접 생성합니다 탬플릿에서 비밀번호용 Generate String Key를 설정하면 CloudFormation에서 비밀번호가 자동 생성됩니다
 
# Cloudwatch logs -encryption
- KMS 키를 사용해 CloudWatch 로그를 암호화할 수 있는데요
- 그리고 CMK를 기존의 로그 그룹과 연결할 수도 있고 혹은 CMK로 새 로그 그룹을 생성할 수도 있습니다
- CloudWatch 로그 API를 사용해야 합니다 우리는 CLI를 사용할 거고요 알아 둬야 할 두 개의 명령으로는 첫 번째로 associate-kms-key KMS 키를 기존의 로그 그룹과 연결하는 명령이고요 혹은 create-log-group을 사용해 아직 존재하지 않는 로그 그룹을 생성해 KMS 키와 바로 연결할 수도 있죠

# codebuild security
- CodeBuild는 VPC 외부에 있지만 VPC 리소스로의 액세스를 위해 CodeBuild를 VPC 내부에서 실행할 수 있습니다
- 환경 변수가 Secrets Manager의 암호를 참조할 수도 있습니다

# aws nitro enclaves
- 클라우드에서 아주 민감한 데이터를 처리해야 할 때가 있을 겁니다, 그럴 때는 독립된 컴퓨팅 환경에서 처리하고 싶겠죠
- Nitro 엔클레이브는 완전 독립형 가상 머신으로 외부와 완전히 차단되어 있습니다
- 암호화 증명을 사용하여 인증받은 코드만 엔클레이브에서 실행되도록 할 수도 있습니다
- 여기에 추가로 KMS 암호화를 적용해 엔클레이브만 민감 데이터에 접근할 수 있도록 설정할 수 있습니다
- EC2에 적용할 수 있는 가장 강력한 보안 방법이죠

- ![image](https://github.com/user-attachments/assets/00e75d08-ad08-4b8d-890d-55d492a282fa)
