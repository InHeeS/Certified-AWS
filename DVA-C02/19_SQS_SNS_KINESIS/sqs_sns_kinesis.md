![image](https://github.com/user-attachments/assets/8da527ea-255d-4a8d-a51e-3515cd0877d2)![image](https://github.com/user-attachments/assets/b0c5fe19-ce02-4e0d-87e6-2e4ed6098703)# messaging 
- 비동기 시스템 및 이벤트 기반 
  - 대기열 모델에는 SQS를
  - pub/sub 모델의 경우 SNS를
  - 실시간 스트리밍을 하고 대용량 데이터를 다룬다면 Kinesis로 분리할 수 있죠
 
# SQS
- 핵심은 대기열 입니다.
- ![image](https://github.com/user-attachments/assets/0f78d064-f43a-4bba-b9ad-a3a2bc0bd3da)
- 시험에서 애플리케이션 분리에 대한 문제가 보이면 Amazon SQS을 생각하시면 됩니다
- 무제한 처리량, 대기열에 있는 메시지 수에도 제한이 없습니다.
- 최대 14일 동안 대기열에 남아 있을 수 있습니다.
- SQS는 대기열 서비스이므로 높은 처리량, 높은 볼륨 등이 있어서 중복 메시지가 있을 수 있습니다
- 생산자는 SDK 소프트웨어 개발 키트를 사용하여 SQS에 메시지를 보낼 겁니다
- 원하는 경우 자체 온프레미스 서버에서 실행할 수도 있고
- AWS Lambda의 람다 함수에서 실행할 수도 있습니다
- 이건 ASG (Auto Scaling groups)와 더불어 SQS를 사용하는 완벽한 사례입니다
- ![image](https://github.com/user-attachments/assets/8a15d45a-9454-42e9-b1c2-253d8757249b)
- ![image](https://github.com/user-attachments/assets/c5956d83-1b5c-47f3-b654-ab20e939ea94)
---
- ![image](https://github.com/user-attachments/assets/75f4f2ed-7ce3-470a-810a-b5878b7bb032)
  - 다른 옵션은 SSE-KMS인데, 고객 마스터 키를 직접 선택할 수 있어요
  - SSE-SQS라는 암호화 유형인데, Amazon S3에 있는 SSE-S3와 비슷해요 서버 측 암호화 옵션 가운데 하나죠

  
- 액세스 제어를 위해 IAM 정책은 SQS API에 대한 액세스를 규제할 수 있고
- S3 버킷 정책과 유사한 SQS 액세스 정책도 있습니다
  - 마지막으로 액세스 정책 탭은 대기열에 접근 가능한 대상과 수단을 보여줘요

# sqs queeu access policy 
- 리소스 정책이라는 점에서 s3 버킷 정책과 유사합니다.
- json iam 정책을 sqs 대기열에 직접 추가 가능합니다.
- 교차 계정 액세스를 허용, 대기열 액세스 정책
  - EC2 인스턴스가 다른 계정의 SQS 대기열에서 가져올 수 있게 합니다
  - SQS 대기열을 조정하거나 S3 이벤트 알림을 게시하는 데 필요한 항목
- ![image](https://github.com/user-attachments/assets/648f3a83-3e6d-4c37-be81-f77aaca8c321)

# sqs message visibility timeout
- 소비자가 메시지를 폴링하면 그 메세지는 다른 소비자들에게 보이지 않게 됩니다
- 즉 가시성 시간 초과 기간 내에서는 그 메시지는 다른 소비자들에게 보이지 않습니다
- 즉 소비자가 메시지를 처리하는 데 시간이 더 필요하다는 것을 알고 있고 해당 메시지를 두 번 처리하고 싶지 않다면 소비자는 ChangeMessageVisibility API를 호출하여 SQS에 알려야 합니다
- ![image](https://github.com/user-attachments/assets/93e2b062-4fdb-43eb-8e6c-0217a3eb9fcb)
  - 소비자가 해당 메시지를 처리할 충분한 시간과, 메시지가 충돌 했을때 sqs 에 나타내는 시간을 고려해야 합니다.
 
# sqs dead letter queue
- 컨슈머가 메시지를 읽다가 실패했거나 시간이 부족해서 메시지가 대기열로 돌아가는 거죠
- 최대 수신 수 임곗값을 설정하면 그 값을 초과했을 경우 SQS에
- FIFO 대기열은 실패 메시지 대기열도 FIFO 대기열이어야 하고
- 표준 대기열은 실패 메시지 대기열 역시 표준 대기열이어야 해요
- 끝으로 실패 메시지 대기열의 메시지들은 대기열에서 만료되기 전에 처리해야 합니다 그래서 기간을 길게 설정하는 게 좋아요

- 실패 메시지 대기열 관리에 쓰는 또 다른 기능은 바로 소스로 재드라이브입니다
- 그러면 컨슈머는 메시지가 실패 메시지 대기열에 들어갔고 이미 메시지 처리 작업을 시도했었다는 것도 모른 채 메시지를 재처리해요
- ![image](https://github.com/user-attachments/assets/0445a824-e088-4bb0-84b7-4e579de0c2c1)

# sqs delay queue
- 대기열 지연은 소비자들이 즉각적으로 보지 못하도록 메시지를 지연시키는 것입니다.
- 15분까지 지연 가능하며 기본값은 0초입니다.

# sqs long polling
- 롱 폴링은 소비자가 sqs 로부터 메시지를 요청할때 대기열이 비어 있다면 메시지가 도착할 때까지 기다리도록 하는 것 입니다.
- API 호출을 줄일 수 있습니다
- 소비자 또한 수신하게 되므로 지연 시간도 줄일 수 있습니다
- 결과적으로 CPU 연산도 적어집니다, 보통은 20초를 선호하죠 그러지 않을 이유가 없으니까요
  
- SQS Extended Client 최대 메시지 크기 제한이 256KB라는 것은 이미 알고 있는데요
- 이를 위해 SQS Extended Client라는 이름의 Java 라이브러리를 사용하여 1gb 크기의 메시지를 보낼 수 있습니다. 
- 즉 SQS 대기열은 작은 메시지를 포함하고 Amazon S3 버킷이 대용량 객체를 포함하죠, 비디오 파일을 처리할 때 사용하며 전체 가 아니라 메타데이터를 포함합니다.
- ![image](https://github.com/user-attachments/assets/cf20ff93-874a-4ce1-9bd7-10f067f7c43c)

- 마지막으로, 여러분은 시험 중에 API 호출을 보게 될 수 있습니다
- ![image](https://github.com/user-attachments/assets/96e8ab30-9755-49f5-b170-c9bdca527dbf)

# sqs fifo queue
- 즉 표준 대기열 보다 더 확실히 순서가 보장되는 것입니다
- 이 SQS 대기열은 제한된 처리량을 가집니다
- 또한 중복을 제거하도록 해주는 SQS FIFO 대기열의 기능으로 인해 정확히 한번만 보낼 수 있게 해 줍니다
- .fifo로 끝나야 합니다

- 심화과정입니다.
- 첫 번째는 중복제거(Deduplication)입니다
  - 즉 5분 간격 이내에 동일한 메시지를 두 번 발송할 경우 두 번째 메시지는 거부되는 것이죠
  - 또는 메시지를 전송할 때 메시지 중복제거 ID를 직접적으로 명시하는 방법도 있습니다
  - 메시지의 SHA-256을 통해 중복제거 ID가 계산될 수 있도록 Content-based deduplication를 활성화 하겠습니다

- 우리가 살펴볼 두 번째 고급 개념은 메시지 그룹화입니다
  - 의무적으로 입력해야 하는 파라미터인 메시지 그룹 ID를 동일한 값으로 정할 경우 해당 ID에 대해서는 한 명의 소비자만 갖게 됩니다
  - 여기서 핵심은 동일한 메시지 그룹 ID를 공유하는 메시지들이 해당 그룹 내에서 순서대로 정렬된다는 것이죠
  - 따라서 SQS FIFO 대기열에서 병렬 처리가 가능한 겁니다
 
# sns 
- ![image](https://github.com/user-attachments/assets/39564d43-5d58-487a-9bc6-e3c50f82e9ca)
- 이것이 Pub/Sub이라고 하는 패턴이죠
- Amazon SNS에서 "이벤트 생산자"는 한 SNS 주제에만 메시지를 보냅니다
- 따라서 SNS 주제 구독자는 해당 주제로 전송된 메시지를 모두 받게 됩니다
- SNS에서 구독자에게 게시할 수 있는 것에는 뭐가 있을까요?
  - 직접 이메일을 보낼 수있고, 모바일 알림, http, https 로 직접 데이터를 보낼 수 있습니다.
  - ![image](https://github.com/user-attachments/assets/0c3b5eaf-c086-454c-90b8-2be1b2d26271)
  - ![image](https://github.com/user-attachments/assets/c94849dc-f697-4d32-92fc-658dd4aaec60)
   - 수신과 송신을 합니다.
- sns 게시 방법으로는 sdk 주제 게시를 사용하거나, 먼저 주제를 만든 다음 하나 또는 여러 개의 구독을 만들고 SNS 주제에 게시하면 됩니다
- 혹은 모바일 앱 SDK 전용 직접 게시 방법이 있는데요 플랫폼 애플리케이션을 만든 다음 플랫폼 엔드 포인트를 만들고 플랫폼 엔드 포인트에 게시하면 됩니다


- 보안 측면에서 Amazon SNS는 SQS와 동일합니다
- 기본적으로 전송 중 암호화와 KMS 키를 사용한 저장 데이터 암호화가 있고 클라이언트 측 암호화가 있는데요
- 액세스 제어는 IAM 정책 중심입니다 모든 SNS API가 IAM 정책으로 규제되기 때문이죠
    - S3 이벤트와 같은 서비스가 SNS 주제에 작성할 수 있도록
 
# sns + sqs fan out
- 개념은, SNS 토픽으로 한 번 메시지를 전송하고 원하는 만큼 SQS 대기열을 SNS 토픽에 구독시키는 것입니다
- 데이터 손실이 없습니다
- ![image](https://github.com/user-attachments/assets/bf102759-024f-47bc-8fcf-681f52e097a0)
- SQS로 데이터 지속성, 지연 처리, 작업 재시도 등의 효과를 얻을 수 있습니다
- 그리고 교차 리전 배송이 있는데 한 리전의 SNS 토픽이 다른 리전의 SQS 대기열에 메시지를 보내는 것이 당연히 가능합니다
- 여러 개의 대기열로 동일한 S3 이벤트 알림을 보내고 싶다면 fan out

- ![image](https://github.com/user-attachments/assets/fa65effa-79ed-4fee-9157-2f98632f1acb)
- ![image](https://github.com/user-attachments/assets/4cf02843-360f-4df6-86e9-7250e2761dff)

---

- 아마존 SNS는 FIFO 기능이 있는데, 토픽의 메시지에 순서를 부여합니다
- SQS FIFO와 같은 기능을 갖는다는 것입니다
- SQS FIFO를 사용해 Fan Out을 할 때는 Fan Out, 정렬, 중복제거가 필요하기 때문입니다
- ![image](https://github.com/user-attachments/assets/fab5f44f-c115-42a7-9e6b-36ef4978efa0)

- 메시지 필터링이란 SNS 토픽 구독자들에게 전송할 메세지를 필터링하는 JSON 정책입니다
- ![image](https://github.com/user-attachments/assets/02318455-78fb-4f9a-9a99-560112792b3e)

# kinesis overview (중요)
- 스트리밍 데이터를 실시간으로 수집, 처리 분석하도록 도와줍니다 애플리케이션 로그, 지표, 웹사이트 클릭스트림, IoT 원격 데이터 등 어떤 것도 실시간 데이터가 될 수 있습니다
- 우리가 매우 상세하게 살펴 볼 Kinesis Data Streams은 데이터 스트림을 입력, 처리하고 저장합니다
- Kinesis Data Firehose는 데이터 스트림을 AWS 내부 또는 외부의 데이터 스토어로 로드하죠
- Kinesis Data Analytics는 SQL 언어 또는 Apache Flink를 통해 데이터 스트림을 분석하기 위한 것이죠
- Kinesis Video Streams를 알아 두면 좋습니다 비디오 스트림을 입력, 처리, 저장하죠

# kinesis data streams 
- 시스템에 빅데이터를 스트리밍 하는 방법
- 번호가 매겨진 여러 샤드로 구성 됩니다. 사전에 프로비저닝 됩니다.
- 데이터 생산자는 아주 좁게 보면 AWS SDK를 활용할 수도 있고요 넓게 보면 Kinesis Producer Library KPL이 될 수도 있죠(lambda 가 될 수 있습니다, 그 밖에 firehose, analytics)
- 애플리케이션 로그를 Kinesis Data Streams로 스트리밍하는 서버 내 Kinesis Agent일 수도 있겠네요
- Kinesis Data Streams 레코드를 생성하죠
- ![image](https://github.com/user-attachments/assets/9b5a857f-8fc7-4ea9-a47d-adc0aa6efbe4)
- 기본적으로 데이터를 재처리하거나 반복할 수 있다는 뜻이죠
- 또한 Kinesis에 삽입된 데이터는 삭제가 불가능합니다
- 같은 파티션 키를 공유하는 메시지는 동일한 샤드로 이동해 키 기반 순서를 제공합니다

- 제작자는 AWS SDK Kinesis Producer Library(KPL) Kinesis Agent를 사용해 데이터를 전송할 수 있습니다
- 소비자는 Kinesis Client Library(KCL) AWS SDK 또는 AWS Lambda Kinesis Data Firehose Kinesis Data Analytics 같은 AWS의 관리형 소비자일 수도 있고요

- 용량 모드가 있습니다.
  - 기록 용량 모드로 프로비저닝 모드이며 수동 혹은 api 사요해 확장
  - 그리고 시간당 샤드 프로비저닝에 따른 비용이 생기죠

  - 온드맨드 모드는 용량을 프로비저닝하거나 관리할 필요가 없는 모드에요 용량이 수요에 맞춰 시간 흐름에 따라 조정되죠
  - 미리 용량이 예측되지 않는다면 온디맨드 모드를

- 리전 내에서만 배포하게 되는데요
- IAM 정책을 사용해 샤드 생성 및 읽기에 대한 액세스를 제어할 수 있습니다
- HTTPS를 이용한 전송 중 암호화 및 KMS를 이용한 저장 중 암호화도 있어요
- 클라이언트측 암호화도 있습니다.
- VPC 엔드 포인트도 Kinesis에 적용할 수 있습니다 인터넷을 거치지 않고 프라이빗 서브넷의 EC2 인스턴스에서

- 마지막으로 CloudTrail로 모든 API 호출을 모니터링할 수 있습니다

# kinesis producers (데이터를 가져오는 방법)
- 생산자를 사용해야 하며 데이터를 데이터 스트림에 보냅니다.
- 샤트 내 파티션 키마다 고유하며
- 생산자는 어떤 sdk 가능하며 kpl 사용하며 c++, java 언어를 지원합니다.
- Kinesis 에이전트는 Kinesis에 데이터를 보내는 다른 방법입니다
- 쓰기 처리량의 경우, 이미 알고 있죠 샤드 당 1초에 1MB 또는 1초에 1,000레코드입니다
- Kinesis에 데이터를 보내는 API는 PutRecord API라고 합니다

- ![image](https://github.com/user-attachments/assets/dc5d4dae-bb6c-4039-be2a-d9d9b8b1b57a)

--- 

- 그러나 만약 한 샤드에 초과 생산하면 예외가 발생합니다 프로비저닝된 처리량을 초과했기 때문이죠 따라서 ProvisionedThroughputExceeded라는 예외가 발생하는데
  - 매우 잘 분산된 파티션 키를 사용하는 것입니다
  - 그리고 기하급수적인 백오프를 통해 재시도를 구현해서 예외 상황을 재시도할 수 있게 해야 합니다
  - 마지막으로 샤드를 스케일링해야 합니다 이를 샤드 분할이라고 합니다

# kinesis consumers
- 소비자는 스트림으로부터 레코드를 가져오고 처리합니다
- 소비자는 람다 함수, Kinesis Data Analytics, Kinesis Data Firehose, SDK를 이용한 사용자 정의 소비자가 될 수 있습니다
- Kinesis Data Streams의 소비자 수에는 한계가 있죠
  - 바로 향상된 팬아웃 소비자입니다 (풀 모델)
    - 전체 소비자에 걸쳐서가 아닌 소비자 당, 샤드 당입니다 2mb/sec, 샤드 하나의 처리량은 초당 6MB가 됩니다
    - ![image](https://github.com/user-attachments/assets/1ee6c33e-2785-40ff-b956-ddf23e4e8237)
    - 소비하는 애플리케이션이 적을 경우에 유용합니다
  - 두 번째 모델은 푸시 모델입니다
    - 지연 시간은 훨씬 적은데 샤드 자체에서 데이터를 소비자로 푸시하기 때문입니다
    - 데이터 스트림 당 5개의 소비자 애플리케이션이라는 제약이 있습니다

- AWS Lambda는 아직 자세히 다루지 않았는데 서버를 이용하지 않고 데이터를 소비하는 방식입니다
  - ![image](https://github.com/user-attachments/assets/499dc8ef-6b89-4e10-808d-52809ca928db)


# kinesis lient dlibrary (kcl)
- 시험에 시나리오 문제로 나옴
- Java 라이브러리는 Kinesis Data Streams에서 읽기 워크로드를 공유하는 분산 애플리케이션의 레코드를 읽을 때 도움을 줍니다
- 각 샤드는 KCL 인스턴스에 의해서만 읽힙니다
- Kinesis 클라이언트 라이브러리는 Kinesis Data Streams로부터 읽습니다
- DynamoDB 덕분에 KCL 애플리케이션의 다른 작업자를 추적하고 샤드에 걸쳐 작업을 공유할 수 있습니다
- ![image](https://github.com/user-attachments/assets/d4bf7ac6-b4b1-4a05-821f-01685856b50d)
  - 스케일 업을 할 때도 마찬가지입니다
  - 샤드보다 더 많은 KCL 애플리케이션을 가질 수는 없는데\
  - DynamoDB와 함께 작업하여 각 KCL 애플리케이션과 샤드 과제 사이의 작업을 분할합니다
  - dynamodb에 체크포인트를 남깁니다.

# kinesis shard splitting
- 샤드 분할, 샤드 병합
- 샤드를 분할하여 더 많은 샤드를 추가하면 초당 1MB의 추가 샤드 당 처리량을 얻습니다
  - Kinesis Data Streams의 비용도 늘어납니다
- Kinesis Data Streams에 오토 스케일링은 없습니다
- 용량을 줄이거나 비용을 절감하고 싶다면 트래픽이 적은 2개의 콜드 샤드를 그룹으로 묶어 하나의 새 샤드로 병합합니다

# kinesis data firehose
- 생산자로부터 데이터를 가져올 수 있는 매우 유용한 서비스이며
- 아마존 CloudWatch 로그 및 이벤트는 Kinesis Data Firehose로 생성될 수 있으며 이러한 모든 애플리케이션은 Kinesis Data Firehose로 레코드를 전송할 것입니다
- 알고 있기 때문에 코드를 작성하지 않고 데이터를 대상에 기록합니다
- Kinesis Data Firehose의 수신처는 세 개가 있습니다
  - s3, redshift, opensearch
- Kinesis Data Firehose는 Datadog, Splunk, New Relic, MongoDB로 데이터를 보낼 수 있으며
- HTTP 엔드포인트가 있는 자체 API가 있는 경우 Kinesis Data Firehose에서 사용자 지정 수신처로 데이터를 보내는 것입니다
- Kinesis Data Firehose는 완전 관리형 서비스입니다
- ![image](https://github.com/user-attachments/assets/25d30644-d888-46d6-91ef-dd5769bd161f)
- 관리가 필요 없으며 자동 스케일링에 서버리스이므로 관리해야 할 서버가 없습니다
- Firehose는 매우 좋은 데이터 가격 모델이며 거의 실시간으로 작동합니다
- 버퍼링이 잘 되어 있으면 Kinesis Data Firehose가 거의 실시간 서비스가 됩니다
- 필요한 경우 람다를 사용하여 자체적인 데이터 변환을 쓸 수 있습니다
- 마지막으로 실패한 모든 데이터나 모든 데이터를 백업 S3 버킷으로 보낼 수 있습니다
  
---

- Kinesis Data Streams는 대규모 데이터를 수집하는 데 사용되는 스트리밍 서비스이며 생산자와 소비자를 위한 사용자 지정 코드를 직접 작성합니다
  - 실시간이므로 200밀리초 또는 70밀리초이며 스케일링은 직접 관리합니다
  - 또한 프로비저닝한 용량에 따라 비용을 지불하게 됩니다
  - 저장 기간은 1일에서 365일까지 설정할 수 있습니다
- 반면 Kinesis Data Firehose는 수집 서비스로 데이터를 S3, Redshift, OpenSearch, 제3자 또는 사용자 지정 HTTP로 스트리밍합니다
    - 서버리스이며 근 실시간입니다
    - 자동 스케일링이 되므로 걱정할 필요가 없습니다
    - 그리고 Kinesis Data Firehose를 통과하는 것만 비용을 지불하게 됩니다
 
# kinesis data analytics for sql applications
- 하나는 SQL 어플리케이션, 다른 하나는 아파치 Flink를 위한 것입니다
- KDA는 두 개의 데이터 소스 사이에 있으며, 각각 키네시스 데이터 스트림과 키네시스 데이터 파이어호스로 데이터를 읽어옵니다
- 둘 중 하나로 데이터를 읽어온 뒤, 실시간 분석을 위해 SQL 구문을 실행합니다
- ![image](https://github.com/user-attachments/assets/7cacbf6a-720a-4ecb-bfef-2abcabd224a9)
- 이 기능은 자동으로 스케일링되며, 실제로 키네시스 데이터 분석을 사용하는 양에 따라
- 출력은 데이터 스트림이나 파이어호스를 사용

---

- Flink를 사용할 때, Java나 Scala, 또는 SQL를 이용해 어플리케이션을 만들어 데이터 처리 및 분석을 할 수 있습니다
- Flink는 특별한 어플리케이션입니다 코드를 필요로 하죠
- ![image](https://github.com/user-attachments/assets/7c6aee5c-9c83-4e16-bd08-edd46ca14a7e)
- 혹시나 해서 말씀드리자면, Flink로 키네시스 데이터 스트림, 아마존 MSK에서는 데이터 읽기가 가능하나 키네시스 데이터 파이어호스는 불가능합니다, 따라서 이를 이용한 실시간 분석이 필요하다면 sql 을 사용해야 합니다. 

- 이 서비스를 통해 자원 계산에 대한 프로비저닝이 가능해지고, 병렬 계산 및 자동 스케일링이 됩니다

# ordering data into kinesis
- Kinesis와 SQS FIFO에서 데이터가 어떻게 정렬되는지 봅시다
- Kinesis가 파티션 키를 해시해서 어느 샤드로 보낼지 결정하지요
- ![image](https://github.com/user-attachments/assets/d988c537-8723-447b-b823-11e147bb9039)

- sqs 같은 경우 중요한 점은 그룹 ID가 많을수록 소비자도 많아진다는 점입니다
- sQS FIFO는 그룹 ID 숫자에 따른 동적 소비자 수를 원할 때 사용하면 좋은 모델입니다
- Kinesis 데이터 스트림을 사용할 경우는 예를 들어 트럭 10,000대가 많은 데이터를 전송하고 또 Kinesis 데이터 스트림에 샤드당 데이터를 정렬할 때입니다

# difference 
- ![image](https://github.com/user-attachments/assets/9bd6a5b3-37eb-48de-8b6e-028bb01badc9)

- 샤드 하나에 소비자당 2 MB/s의 속도가 나옵니다 처리량이 훨씬 높을 테니 Kinesis 스트림에서 더 많은 애플리케이션 읽기가 가능하죠
- 따라서 실시간 빅 데이터 분석, ETL 등에 활용되죠 샤드 레벨에서 정할 수 있어미리 Kinesis 데이터 스트림마다 원하는 샤드 양을 지정해야 합니다
- 물론 온디맨드 용량모드에선느 자동으로 조정 됩니다. 
