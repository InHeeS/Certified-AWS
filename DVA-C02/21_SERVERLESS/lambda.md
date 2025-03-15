![image](https://github.com/user-attachments/assets/9ebbef04-e705-46a1-a244-0587218ad4e8)# serverless
- 관리할 필요가 없는 겁니다 그냥 코드를 배포하지요 즉 기능을 배포하는 겁니다
- 서버리스란 것은 서버가 없는 게 아니라 서버가 보이지 않거나 서버를 프로비전 하지 않은 것을 의미하죠
- Aurora 서버리스는 그 데이터베이스가 관리 서버 없이도 온디맨드로 스케일링되지요
- ![image](https://github.com/user-attachments/assets/3250ee44-9e9b-4c92-a25e-10c3dce5bb93)

# Lambda
- ![image](https://github.com/user-attachments/assets/f966da0c-1676-41d3-b660-a508949b9b5f)
- 마지막으로 함수당 더 많은 리소스를 프로비저닝하고 싶다면 함수당 최대 10기가바이트의 RAM을 프로비저닝할 수 있습니다 이는 상당히 많은 양입니다
- 함수의 RAM을 늘리면 CPU와 네트워크의 품질과 성능도 향상됩니다
- EKS 또는 Fargate라는 서비스가 있다는 걸 기억해두시기 바랍니다
- 가장 중요한 언어는 확실히 Node.JS와 Python입니다
- API 게이트웨이는 Rest API를 생성하고 Lambda 함수를 호출하는 것입니다
- Kinesis는 Lambda를 사용하여 실시간으로 데이터 변환을 수행할 것입니다
- DynamoDB는 몇 가지 트리거를 생성하는 데 사용될 것입니다
- SNS 주제에서 알림에 반응하기 위해 SNS를 사용할 수 있습니다
- SQS 큐에서 메시지를 처리하기 위해 SQS를 사용할 수 있습니다
- ![image](https://github.com/user-attachments/assets/03836062-687f-4d5e-a383-1d66f51aa35f)
- ![image](https://github.com/user-attachments/assets/6e7d5a15-5142-47de-9459-0f0a901a5e07)
- 처음 100만 건의 요청은 무료이며 추가 100만 건의 요청당 $0.20를 지불하게 됩니다
- 따라서 매월 40만 기가바이트 초의 컴퓨팅 시간을 무료로 얻을 수 있습니다

# lambda synchronous invacations
- 여러분이 CLI, SDK API 게이트웨이 혹은 애플리케이션 로드 밸런서를 사용하고 계시다면 동기식 호출을 쓰시는 겁니다
- 동기식이라는 말은 여러분이 결과를 기다리게 되는 직접 호출임을 의미합니다
- 그러니 애플리케이션 로드 밸런서를 통한 일라스틱 로드 밸런싱 API 게이트웨이, Lambda@Edge를 사용하는 CloudFront겠죠

# lambda integration with alb
- 첫 번째 방법은 애플리케이션 로드 밸런서를 쓰는 것이고
- 또 하나는 다음 강의, 다음 섹션에서 보게 될 API 게이트웨이를 쓰는 겁니다
- 여기서 질문은 "어떻게 로드 밸런서가 HTTPS 요청을 람다 호출로 바꾸는가?" 입니다
- 이와 유사하게 람다 함수도 JSON 문서로 된 뭔가를 보냅니다 그럼 ALB가 이를 다시 HTTP로 변환해 주겠죠
- ALB가 HTTP를 JSON으로, 다시 그 역으로 변환하는 방식을 알았습니다
- 우리는 ALB 설정을 통해 다중 헤더 값을 갖도록 할 수 있습니다
- 이 경우, name=foo와 name=bar는 이름은 같지만 값이 다릅니다 그렇다면 설정을 활성화하여 두 헤더와 쿼리 스트링 매개변수를 람다 함수로 된 배열로 변환해 줄 수 있습니다
- ![image](https://github.com/user-attachments/assets/65b52661-2478-49a9-80b6-3bf03e506243)

# Lambda Asynchronous invacaitons
- Amazon S3, SNS 주제들 CloudWatch 이벤트 등이 있습니다
- 람다 함수의 멱등성이 존재
- ![image](https://github.com/user-attachments/assets/4ac6328c-2169-4058-a95d-7734a549a71e)
- 비동기식 호출은 속도를 높이거나 비동기식 호출만 사용가능한 서비스가 존재
- CodeCommit이 있는데 새 브랜치, 태그, 푸시가 있을 때 람다 함수를 트리거링합니다
- s3, sns cloudwatch event, cloudwatch eventbridge ses, cludformation, config iot, iot event
- ![image](https://github.com/user-attachments/assets/29d2d743-e2af-4aac-96f6-e134e2ca180f)
  - eventbridge 로 귳기 생성 후 람다 함수 작업 생성

# s3 event notifications
- 다를 통해서 S3 이벤트 알림을 통합
- 접두어와 접미어로 필터링이 가능하고요
- ![image](https://github.com/user-attachments/assets/8238a845-2d2b-4dfa-b064-00eaa3988a7d)
  - 버킷 버저닝 활성화 

# lambda - event source mapping 
- 이는 Kinesis 데이터 스트림 SQS 및 SQS FIFO 대기열과 DynamoDB 스트림에 사용됩니다
- ![image](https://github.com/user-attachments/assets/c2ce5456-7136-45c6-867c-755acf694b10)
  - 이벤트 배치를 통해 람다 함수를 동기적으로 호출할 겁니다
- 스트림은 이벤트 소스 매핑이 일어나 각 샤드에 대한 반복자를 생성합니다
- Kinesis에서든 DynamoDB에서든 샤드에서 아이템이 처리될 때 아이템은 스트림으로부터 제거되지 않으며 Kinesis와 DynamoDB의 다른 소비자들이 데이터를 읽을 수 있습니다
- 만약 트래픽 스트림이 낮다면 배치 윈도우를 사용해서 처리 전에 레코드를 축적합니다
- ![image](https://github.com/user-attachments/assets/e416b6d5-a623-436f-970c-2040bc1618db)
  - 병령 처리 가능합니다. 람다 함수와 스트렘에서
- ![image](https://github.com/user-attachments/assets/db8121f9-ab38-4d2a-bf1c-7e7b54e33535)
  - dlq 를 람다가 아닌 sqs 대기열에 신청해야함 람다용 dlq 는 비동기식 호출에만 작동
- FIFO 대기열, 즉 선입선출을 사용하는 경우에는 람다가 순차적 처리를 지원하는데요
-  SQS 표준에서는 람다가 매우 빠르게 스케일링된다고 했었죠 스케일 업을 위해 추가하는 인스턴스가 분당 16개니까, 제법 빠른 거죠 또한 초당 동시에 처리되는 배치량은 최대 1,000개입니다
-  sqs fifo 와는 조금 다릅니다. 그룹 id가 같은 메시지는 무조건 순서대로 처리되고 람다 함수는 활성 메시지 그룹의 수만큼 스케일 업합니다. 

# lambda event and context objects
- 이벤트 객체 외에도 컨텍스트 객체란 것도 있습니다.함수에 대한 메타데이터가 포함되어 있고 
- 가령 함수의 AWS 요청 ID, 함수 이름, 함수의 로그 그룹, 사용 가능한 메모리 등이 포함됩니다
- 이벤트 객체는 JSON 형태의, 함수가 처리할 데이터를 갖고 있는 문서입니다 Eventbridge나 아마존 SQS, 아마존 SNS와 같이, 호출되는 서비스는 람다 함수가 이벤트를 처리할 때 필요한 모든 정보를 갖는 셈이죠
  - 런타임에 따라서 이벤트 객체는 다른 객체로 변환 됩니다. 
- 컨텍스트 객체는 런타임 환경과 호출 자체의 데이터를 제공합니다. 람다가 이 객체를 함수에 전달하고 AWS 요청 ID, 함수 ARN과 이름, 사용 메모리 한도가 있으며 Cloudwatch 로그의 로그 스트림명, 로그 그룹명과 같은 것이 포함되어 있습니다

# lambda destinations
- 비동기식 호출에서는 성공 및 실패한 이벤트 모두에 목적지를 정의할 수 있는데요
- 눈치를 채셨을 수도 있지만 비동기식 호출의 DLQ 설정과 굉장히 유사한 형태입니다
- DLQ는 SQS와 SNS로 실패만 전송하도록 하지만 목적지 기능은 성공과 실패 결과 모두가 SQS, SNS, 람다 및 EventBridge로 전송되게끔 허용이 되니까요
- 이벤트 소스 매핑의 경우는 이 경우는 처리할 수 없는 이벤트 배치를 폐기하는 경우에 사용이 됩니다
  - Kinesis 데이터 스트림 전체를 차단하는 대신 폐기된 이벤트 배치를 실패한 이벤트 목적지로 전송하기만 하면 됩니다
  - 그러니 이벤트 소스 매핑이 SQS에서 읽기를 하고 있다면 실패한 목적지를 설정하거나, 아니면 DLQ를 SQS에 직접 설정하시는 것 잊지 마세요

# lambda execution roles(iam role)
- IAM 역할은 람다 함수에 연결되어 있어야 하며
- AWS 서비스와 리소스에 액세스할 수 있는 권한을 갖습니다
- 람다 VPC 액세스 실행 역할은 VPC 내부에 람다 함수를 배포할 수 있도록 해주죠
- 이벤트 소스 매핑으로 함수를 호출할 때마다 데이터를 읽는 것이 바로 람다입니다
- 람다 함수가 다른 서비스를 통해 호출되는 경우에는 리소스 기반 정책이 사용됩니다
- Lambda에는 환경 변수라는 것이 존재하는데, 이때 환경 변수란 이는 문자열 형식의 키-값 쌍으로 코드를 업데이트하지 않고도 함수 행위 특성을 조정할 수 있습니다
- 환경 변수를 KMS 등으로 암호화하면 비밀 값을 저장할 수도 있다는 겁니다

- 해당 비밀 값은 Lambda 서비스 키나 고객 마스터 키(CMK)로 암호화됩니다

# lambda logging & monitoring
- Lambda의 모든 실행 로그는 자동으로 CloudWatch Logs에 저장됩니다
- CloudWatch Logs에 기록될 수 있도록 권한을 주는 알맞은 IAM 역할이 있어야 하죠
- Lambda 함수에서는 X-Ray를 이용한 추적이 가능합니다
- AWSXRayDaemonWriteAccess라는 관리 정책이 따로 있습니다

# customization at the edge
- 함수와 애플리케이션을 특정한 리전에 배포한다고 배웠는데 CloudFront 등을 사용할 때는 엣지 위치에 콘텐츠를 배포하는 경우도 있습니다
- 이를 엣지 함수라고 부르고요, 이 코드는 작성하여 CloudFront 배포 시
- 지연 시간을 최소화 함
- 엣지 함수를 사용하면 서버를 관리하지 않아도 되고 엣지 함수는 전역적으로 배포되는데요, 예를 들어 CloudFront에서 제공되는 CDN 콘텐츠를 사용자 지정하는 사용 사례가 있습니다
- 검색 엔진 최적화(SEO), 오리진과 데이터센터 간 지능형 라우팅을 수행할 수도 있으며 엣지에서의 봇 완화, 엣지에서의 실시간 이미지 변환, A/B 테스트, 사용자 인증과 권한 부여, 사용자 우선 순위 지정, 사용자 추적 및 분석 등이 가능합니다
---
- CloudFront 함수는 뷰어 요청 및 응답에만 해당되어 수준이 높고 규모가 크다는 점을 기억하세요
- Lambda@Edge는 조금 더 자세해서 모든 걸 수정할 수 있어요
- CloudFront 함수는 JavaScript만 가능하고

- Lambda@Edge에는 NodeJS와 파이썬이 가능하며 규모 측면에서는 CloudFront 함수가아주 큽니다
- Lambda@Edge는 뷰어와 오리진 모두에 영향을 미치는 반면 CloudFront 함수는 뷰어에만 영향을 미치고요,
- CloudFront 함수에서는 최대 실행 시간이 1밀리초 미만으로 아주 빠르고 간단한 함수인 반면 Lambda@Edge의 경우 5초에서 10초까지 걸릴 수 있죠
- 다른 AWS 서비스에 액세스해야 할 때 AWS SDK를 사용하는 거예요
- Lambda@Edge를 통해 파일 시스템 액세스나 HTTP 요청의 본문 액세스도 획득할 수 있어요

# lambda by vpc
- 기본적으로 Lambda 함수는 여러분 자체 VPC 외부에서 실행됩니다
- 이를 위해서는 VPC ID와 서브넷을 정의하고 Lambda 함수에 보안 그룹을 배정해야 합니다 -> 내부 vpc 에 접근이 가능하다.
- ![image](https://github.com/user-attachments/assets/51bd353b-06eb-401c-b105-5ca9555655ab)
- 기본적으로 VPC 내에 있는 Lambda 함수에는 인터넷 액세스 권한이 없습니다
- Lambda 함수를 공용 서브넷에서 배포한다고 해도 인터넷이나 공용 IP 접근 권한을 얻을 수 없다는 점 알고 계시기 바랍니다
- 배포해서 인터넷 액세스 권한을 얻으려면 NAT 게이트웨이나 NAT 인스턴스를 사용해야 합니다
- 외부 API에 액세스하려면 NAT 디바이스의 공용 서브넷을 거쳐야 합니다
- DynamoDB에 사설 경로를 통해 액세스하려면 VPC 엔드 포인트를 사용하면 됩니다
- ![image](https://github.com/user-attachments/assets/9716f1f7-f0f1-4be6-a4b3-4fc00c3c59b6)

# lambda function configuration(성능)
- 1MB씩 증가시켜서 10GB까지 확장할 수 있어요
- Lambda 함수에 RAM 또는 메모리를 더 추가하면 여러분이 받게 되는 vCPU 크레딧도 많아지게 되고요
- 여러분의 함수가 실행되는 시간을 줄이고 싶다면 Lambda 함수의 RAM을 늘려야 해요
- 여러분의 Lambda 함수는 기본값으로서 타임아웃이 3초로 되어 있어요
- 하지만 여러분은 타임아웃을 최대 900초, 즉 15분까지로 설정할 수 있어요
- 핸들러의 외부에서 데이터베이스 접속을 초기화하는 방식이에요 왜일까요? 왜냐면 접속이 한 번만 초기화될 거고요, 함수를 호출할 때마다 재사용할 수 있기 때문이죠
- 속 클라이언트나 HTTP 클라이언트 또는 SDK 클라이언트가 열리는 곳을 기준으로 어떤 게 좋은 것인지 알아맞히도록 요구할 거예요
- /tmp 임시 파일은 이때 여러분은 Lambda 함수를 위해 10GB의 디스크 공간을 얻게 되고요
  - 여기서 우리는 0.5GB까지의 아주 큰 파일을 /tmp 공간에 기록할 수 있어요
  - kms 피처를 사용해서 데이터 키를 생성하고 /tmp 공간에 있는 데이터를 암호화 해야합니다.
 
# lambda layers
- 람다의 사용자 지정 런타임 생성
- 목표는 아주 자주 변경될 수 있는 애플리케이션 패키지의 코드를 외부화하는 거예요
- 그러니 함수를 훨씬 빠르게 배포할 수 있고 종속성이 있을 때마다 매번 다시 패키징할 필요가 없고요

# lambda - file dsystems mounting
- 그러려면 초기화 과정에서 EFS 파일 시스템이 로컬 디렉터리에 마운트되도록 Lambda를 구성합니다
- 잘 작동하려면 EFS 액세스 포인트 기능을 사용해야 하는데요
- ![image](https://github.com/user-attachments/assets/938a1f56-9c5a-4ae5-aba2-577c6514fb3a)
- 임시 스토리지 /tmp는 최대 10GB에 달해서 무척 큽니다
- Lambda 함수에 최대 512MB까지 포함되고 512MB를 더 사용하려면 추가 비용을 지불합니다
- Lambda 계층의 경우 최대 크기는 함수당 계층 5개, 250MB까지라 최대
- Lambda 계층의 데이터는 수정할 수 없다는 점 기억하세요
- Amazon S3 액세스 권한을 얻으려면 적절한 IAM 권한이 필요합니다
- 마지막으로, 네트워크 파일 시스템이라서 Lambda 호출 전체에서 공유됩니다

# lambda concurrency and throttling
- 람다 함수가 최대 1,000개까지 동시에 실행되어
- 비동기식 호출의 경우에는 자동으로 재시도한 후 DLQ로 이동하죠
- 동시성 제한이 여러분 계정의 모든 함수에 적용되므로 하나의 함수가 이 제한을 초과해도 다른 함수까지 조절 대상이 된다는 점을 꼭 기억하시기 바랍니다
- ![image](https://github.com/user-attachments/assets/7aafd644-4a0d-42d9-8eb4-d743527ca1f8)
- 그리고 이 재시도 간격은 지수 백오프 방식으로 증가합니다

- 콜드 스타트는 새로운 람다 함수 인스턴스를 생성할 때에 코드가 로드되어야 하며 핸들러 외부에서 이 코드가 실행되어야 함을 의미합니다
- 프로비저닝된 동시성을 사용할 수 있습니다 (해결 책)
- 함수가 호출되기도 전에 동시성을 할당해 놓는 것이죠

# lambda function dependencies
- 즉 언어마다 종속성을 패키징하는 각각의 방법이 있는 거죠 하지만 꼭 기억해야 할 점은 모두 압축해야 한다는 것입니다
- 그래서 AWS SDK를 사용하는 경우엔 코드에 SDK를 패키징하지 않아도 됩니다
- 람다 함수가 외부 라이브러리에 의존하는 경우, 예를 들어 X-Ray SDK, Database Clients 등이 되겠죠

# lambda and cloudformation - inline
- cloudformation 을 활용하여 람다 함수를 업로드
- 인라인은 람 다 코드를 인라인으로 cloudformaiton 템플릿으로 정의
- 하지만 인라인 함수에서 중요한 것은 함수 종속성은 포함시킬 수 없다는 겁니다 -> 간단한 경우만 사용가능(종속성이 없음 람다 함수 코드를)
- s3 의 경우 버저닝을 하는것이 업데이트에 용이
- 마지막으로 람다 함수를 CloudFormation을 통해 다수의 계정에 배포하고 싶은 경우
- ![image](https://github.com/user-attachments/assets/8a717546-d323-4948-acdc-28ec33815f4c)
  - s3 버킷정책 활용


# lambda container images
- 새로운 이 기능으로 Lambda 함수를 ECR에서 10GB까지
- 베이스 이미지는 Lambda 런타임 API를 통해 구현해야 합니다
- Amazon ECR에 전송한 다음 Amazon ECR에서 Lambda로 컨테이너를 배포할 수 있다는 겁니다
- build images
  - ![image](https://github.com/user-attachments/assets/125c5480-e1e3-42c7-9060-26d47abc58e2)
- 마지막으로, 계층 규모가 큰 함수에는 리포지토리를 하나만 사용해야 해요
- Amazon Linux 2에 구축된 AWS에서 제공된 베이스 이미지를 사용하는 겁니다

# lambda versions alias
- 각 람다 함수의 버전은 $LATEST와 마찬가지로 액세스가 가능합니다
- 하지만 최종 사용자에게 안정적인 엔드 포인트를 제공하려면 어떻게 할까요? 이를 위해서는 람다 별칭을 쓰면 됩니다
- 별칭은 블루/그린 배포를 가능하게 하는데 그럼 왜 별칭을 사용하는 걸까요?
- 따라서 별칭은 저마다 고유의 ARN을 갖게 됩니다
- 별칭은 오직 버전만 참조할 수 있는데 아마 시험에서 이게 함정으로 나올 겁니다 -> 별칭이 다른 별칭을 참조 할 수 없음

# lambda & codedeploy
- 코드 통합에 대한 내용입니다, CodeDeploy를 사용하면 Lambda alias에 트래픽 이동을 자동화할 수 있고, 버전과 별칭에 빌드합니다
- CodeDeploy로 Lambda를 배포하기 위해 AppSpec.yml을 사용하는 경우에는 몇 가지 중요한 매개변수가 있어요
- name, targetversion, currentverison

# lambda function url 
- 함수를 사용하면 Lambda 함수에 절대로 바뀌지 않는 고유한 URL 엔드포인트가 주어지는데요,-> 퍼플릿 인터넷에서 액세스 가능
- Lambda 함수를 특정량까지만 실행하고 싶어서 쓰로틀링이 필요하다면 Lambda의 예약된 동시성 기능을 사용해 Lambda 함수 실행량을 제어할 수 있어요
- 리소스 기반 정책의 lambda 는 cors 보안을 꼭 설정해야 합니다.
- 같은 계정 내에 있다면 자격 증명 기반 정책이나 리소스 기반 정책 둘 중 하나로 API 호출을 허용하면 됩니다
- S3 버킷이랑 아주 비슷한데요, 교차 계정을 사용하는 경우에는 자격 증명 기반 정책과 리소스 기반 정책 모두가 허용되어야 합니다 (a 리소스, b 계정 ㅇ자격 증명 기반 

# lambda and code guru profiling
- CodeGuru 사용 시 CodeGuru는 Lambda 함수의 프로파일러 그룹을 만들고 Java, Python 런타임에 
- 이 통합, 프로파일링을 활성화하면 CodeGuru 프로파일 계층이 Lambda 계층으로 함수에 추가돼요
- iam 권한이 필요합니다.
- Amazon CodeGuru 프로파일러 에이전트 액세스 정책이 함수 IAM 역할에 추가되죠

# lambda exam!
- 이러한 제한은 지역에 따라 다르기 때문입니다
- 128MB에서 10GB까지이며 이것은 1MB 단위로 증감합니다
- 최대 실행 시간은 900초 즉 15분입니다
- 환경 변수는 최대 4KB로 아주 작은 용량입니다
- /tmp 폴더 안의 이 용량은 512MB입니다
- 동시 실행은 최대 1000건입니다.
- 마지막으로 배포의 경우 zip 압축파일의 최대 크기는 50mb입니다
- 그 이상의 파일, 예컨대 대형 파일일 경우에는 /tmp space를 대신 사용해야 합니다

# lambda best practices 
- 즉, 함수 핸들러 외부에서 데이터베이스를 연결하고 핸들러 외부에서 AWS SDK를 초기화하고 함수 핸들러 외부에서 종속성과 데이터셋을 가져와야 합니다
- 시간이 지남에 따라 변화하는 모든 것에 대해 환경 변수를 사용해야 합니다
- 즉, 데이터베이스 연결 문자열, S3 버킷 이름 등등을 코드에 값으로 입력하지 마세요
- KMS를 이용하여 암호화하여 환경 변수로 사용할 수 있습니다
- 그리고 배포 패키지 크기를 런타임에 맞게 최소화해야 합니다
- 어떤 라이브러리를 재사용해야 한다면 람다 계층을 생각해 보세요
- Lambda 함수가 자기 자신을 호출해서는 안 됩니다 recursive x


