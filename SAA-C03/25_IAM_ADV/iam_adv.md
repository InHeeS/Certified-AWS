# Organizations

글로벌 서비스로 다수의 aws 계정을 동시에 관리합니다. 

메인 계정이 관리계정이 됩니다. 멤버 계정은 한 조직에만 소속됩니다. 

장점으로는 모든계정의 비용을 통합 결제 가능합니다. 

조직내 모든 계정에 대해 집계된 사용량에 기반한 비용 할인을 받을 수 있습니다. 

비즈니스 루트 조직단위(ou) 라는 개념이 있습니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/22307693-492f-4028-add2-29f9382c8afb)

장점은 다수의 계정을 가지므로 다수의 vpc 를 가진 당리 계정에 비해 보안이 뛰어납니다. 

한번에 모든 계정에 대해 cloudtrail 을 활성화해서 모든 로그를 중앙 s3 계정으로 전송 가능합니다. 

cloudWatch logs 를 중앙 로깅 계정으로 전송 가능합니다.

보안 측면에서는 서비스 제어 정책, 즉 scp를 정의 가능합니다. 
이는 특정 OU 또는 계정에 적용되는 IAM 정책으로
해당 사용자와 역할 모두가 계정 내에서 할 수 있는 일을 제한합니다
SCP는 모든 대상에 적용되나
영구적인 관리자 권한을 갖는 관리 계정에는 적용되지 않습니다

scp 에는 '차단 목록' 과 '허용 목록' 두가지 전략이 있습니다. 

Allow, *(별표)로 모든 서비스의 모든 작업을 허용한 다음
Deny 문을 추가해
DynamoDB에 대한 액세스를 거부하는 식입니다

허용 목록은 모든 액션을 허용하지 않고
가령 EC2와 CloudWatch만 허용하는 방식입니다

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/55715449-f40c-4d7e-8547-a945e469ecb3)

children 의 정책을 상속의 개념으로 연결됩니다. 

# iam advanced

aws:SourceIp
-> api 호출이 생성되는 클아이언트 ip를 제한하는 데 사용되는 조건입니다. 

두개의 ip 주소 범위에 포함되지 않는 ip 주소는 거부하는 겁니다. 

예를 들어 회사 네트워크에만 aws를 사용하도록 제한하여 회사 내에서만 aws 접근이 가능합니다. 

다음 조건은 aws:RequestedRegion입니다
-> AWS로 시작하므로 글로벌 조건이며 API 호출 리전을 제한합니다

다음은 ec2:ResourceTag입니다 태그의 접두사가 EC2이죠
-> 따라서 이 조건은 EC2 인스턴스의 태그에 적용됩니다

멀티팩터 인증을 강제하는 aws:MultiFactorAuthPresent도 있습니다
->정책을 보면 사용자는 EC2에서 모든 작업을 할 수 있지만

멀티팩터 인증이 있어야만 인스턴스를 중지하고 종료할 수 있습니다

MFA가 false(거짓)일 때 거부되죠

이제 S3 버킷에 대한 IAM 정책을 알아보겠습니다
-> ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/41c088c8-0d42-4118-975c-ffadd72e0b87)

aws:PrincipalOrgID와 리소스 정책을 살펴봅시다
-> AWS 조직의 멤버 계정에만 리소스 정책이 적용되도록 제한합니다

# iam roles & resource based policies

교차 계정의 경우 가령 s3 버킷 교차 계정에서 api 호출을 수행하려면 두가지 방법이 있습니다. 

첫 번째는 IAM 역할이 S3 버킷에 액세스했고

두 번째는 S3 버킷 정책이 사용자의 액세스를 허용했죠

차이가 뭘까요?

*역할을 맡으면
기존의 권한을 모두 포기하고
해당 역할에 할당된 권한을 상속하게 됩니다
매우 중요합니다*

*대신 리소스 기반 정책을 사용하면 본인이 역할을 맡지 않으므로
권한을 포기할 필요가 없습니다*

Amazon EventBridge에는 원하는 작업을 하려면

대상에 대한 권한을 요구하는 규칙이 있습니다
리소스 기반 정책을 지원하는 대상에는 Lambda, SNS, SQS
CloudWatch Logs API Gateway 등이 있습니다
이 경우 대상 리소스를 변경해야 해요 리소스 기반 규칙을 적용해

두 번째 유형은 Kinesis Data Streams
Systems Manager Run Command나 ECS 태스크를 시작할 때는
IAM 역할이 필요합니다
IAM 역할을 EventBridge 규칙에 추가하면
Kinesis에 쓰기 작업을 수행할 권한을 갖게 됩니다

# iam permission boundaries

권한 경계는 사용자워 역할만 지원하고 그룹은 지원하지 않습니다. 

iam 개체의 최대 권한을 정의하는 고급 기능입니다. 

--

iam 권한 경계를 생성하기 전에 사용자를 생성합니다. 

그런 다음 사용자에 대한 권한 경계를 설정합니다. 
권한 경계를 만들도록 정책을 할당합니다. 
John에게 AdministratorAcess가 있어도
권한 경계인 S3 내부에만 액세스할 수 있어요

즉 관리자 액세스를 부여하는 정책을 연결했음에도
권한 경계 때문에 제한이 생기는 겁니다

IAM 권한 경계는 AWS Organizations SCP와 함께 사용될 수 있어요

권한 경계 내에서 새 IAM 사용자를 생성하거나
개발자가 권한을 스스로 부여하고 관리하는데
특권을 남용하는 것을 막기 위해
스스로를 관리자로 만들지 못하게 할 수 있어요

또 계정에 SCP를 적용해서 계정의 모든 사용자를 제한하지 않고
AWS Organizations의 특정 사용자만 제한할 수도 있죠

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/c86d05ce-cbe3-4822-9503-c252781047db)

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/12b6ccaa-c78a-40e2-b511-51d69192427e)


sqs:*에 Deny가 명시되어 있기 때문에
sqs:* 안에 있는 sqs:DeleteQueue는
Allow가 있음에도 자동으로 거부됩니다

# aws iam 자격 증명 센터

싱글 사이온은 한 번만 로그인 하면 됩니다. 

조직이나 비즈니스 클라우드 애플리케이션의 모든 aws 계정에서 사용 가능합니다. 

여러 aws 계정에 로그인 가능 합니다. 

작동 방식

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/58787a02-31cb-4048-8fc1-90358d29a1cb)


![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/760231e4-b21a-42ec-a307-e160ee690f86)


IAM 자격 증명 센터를 통해 로그인해서 Dev 계정 또는 Prod 계정의 콘솔에 액세스할 때

해당 계정에서 IAM 역할을 자동으로 위임합니다

# microsoft active directory & aws directory service

Microsoft AD는 AD 도메인 서비스를 사용하는
모든 Windows 서버에 사용되는 소프트웨어입니다

또한 중앙 집중식 보안 관리로
계정 생성, 권한 할당 등의 작업이 가능합니다
모든 객체는 트리로 구성되며 트리의 그룹을 포리스트라고 합니다

사용자는 어떤 단일 머신에서도 액세스할 수 있습니다

--

AWS Directory 서비스를 살펴보겠습니다
AWS에 액티브 디렉터리를 생성하는 서비스로 세가지의 서비스의 차이점을 알아야합니다. 


1. 독립 실행형 Active Directory로 온프레미스와 AWS 액티브 디렉터리 간에 사용자가 공유되는 셈입니다

2. AD 커넥터는 디렉터리 게이트웨이, 다시 말해 프록시로 온프레미스 AD에 리다이렉트하며MFA, 즉 멀티팩터 인증을 지원합니다 사용자는 온프레미스 AD에서만 관리되고요

3. Simple AD 는 Microsoft 디렉터리를 사용하지 않으며 온프레미스 AD와도 결합되지 않습니다
온프레미스 AD가 없으나

AWS 클라우드에 액티브 디렉터리가 필요한 경우
독립형인 Simple AD 서비스를 사용합니다

*정리*

온프레미스에서 사용자를 프록시한다면 AD 커넥터가 필요하고

AWS 클라우드에서 사용자를 관리하고 MFA를 사용해야 할 때는

AWS 관리형 AD가 필요합니다

온프레미스가 없을 때는

--

IAM Identity Center와 액티브 디렉터리 통합 방법을 알아보죠

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/bbf38c24-91f8-4662-919d-eee91fd3500b)
 

클라우드에 있는 액티브 디렉터리에서

사용자를 관리하고 싶다면

첫 번째 솔루션이 낫고 API 호출만 프록시하려면

두 번째 솔루션이 적합합니다 지연 시간이 길어지긴 하지만요

Simple AD를 선택하면 됩니다

# AWS Control Tower 

서비스의 장점 클릭 몇번으로 환경을 자동으로 설정합니다. 

원하는 모든 것을 미리 구성이 가능합니다.

가드레일을 사용해 자동으로 지속적인 정책 관리를 할 수 있습니다. 

대화형 대시보드를 통해 모든 계정의 전반적인 규정 준수를 모니터링 합니다

그럼 가드레일은 무엇일까요? AWS에서 다중 계정을 설정한다고 해보죠
이때 특정 항목에 관해 한 번에 모두 제한하거나
특정 유형의 규정 준수 사항을 모니터링하고자 합니다
이때 가드레일을 사용하면 Control Tower 환경 내의
모든 계정에 관한 거버넌스를 얻을 수 있습니다

가드레일은 두 가지 유형이 있는데요 첫 번째는 예방(Preventive) 가드레일입니다
계정을 무언가로부터 보호하는 것이므로 제한적이기 때문에
AWS Organization 서비스의 서비스 제한 정책인
SCP를 사용해 모든 계정에 적용합니다

두 번째 유형의 가드레일은 탐지(Detective) 가드레일입니다

규정을 준수하지 않는 것을 탐지하는 것입니다
규정 준수에 대해 말씀드린 것처럼 AWS Config 서비스를 사용합니다
가령, 계정에서 태그가 지정되지 않은 리소스를 식별한다고 하면
Control Tower로 탐지 가드레일을 설정하고
AWS Config를 사용하면 모든 멤버 계정에
Config가 배포되어
규칙과 태그가 지정되지 않은 리소스를 모니터링 하는데

규정을 준수하지 않으면 SNS 주제를 트리거 해서
관리자로서 알림을 받거나 SNS 주제도 Lambda 함수를 실행해
Lambda 함수가 자동으로 문제를 교정합니다
즉, 태그가 지정되지 않은 리소스에 태그를 추가하겠죠

# quiz

Question 5:
EventBridge 권한 설정할 때 Lambda 함수를 대상으로 설정하려면 …………………..를 사용해야 하지만, Kinesis Data Streams을 대상으로 설정하려면 …………………..를 사용해야 합니다.

Answer : 리소스 기반 정책, 자격 증명 기반 정책 




   
