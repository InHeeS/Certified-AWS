# 시나리오

1.
Cognito 는 aws sts 를 통해 임시 자격 증명을 제공할 수 있습니다. 그리고 이 자격 증명을 모바일 클라이언트에게 반환합니다. 이 가격증명은 클라이언트가 s3에서 파일을 저장하고 회수하며 s3 에서 전용 공간에 액세스하도록 허용해야합니다. 

*오답으로는 'AWS 사용자 자격 증명을 모바일 클라이언트에 저장한다' 입니다.*

*정답은 Cognito 와 STS, S3를 임시 자격 증명에 사용해야합니다. 

읽기 처리량을 늘리면서 전체 비용을 줄이려면 아키텍처를 어떻게 바꾸어야 할까요?

캐싱 계층으로 dax를 사용하면 됩니다. 

Cognito 를 이용하면 똑같은 애플리케이션 패턴으로 애플리케이션이 dynamoDB 나 Lambda에 직접 액세스 가능합니다. 

dax로 dynamoDB 상에서 읽을 내용을 캐시에 저장하면 손쉽게 활성화 하면서 성능을 개선하고 비용을 절감할 수 있습니다. 

REST 요청 캐싱은 응답이 고정된 경우 API Gateway 레벨에서 가능합니다. 마지막으로 보안은 전부 Cognito로 가능한데 API Gatewayd와 직접 통합되어 있습니다. 

2. 서버리스로 호스팅 되는 웹사이트, 글러볼 스케일 아웃이 가능해야합니다. 즉 읽는 빈도가 높은 시나리오

대부분 정적파일로 구성되며 동적인 REST API로 구성됩니다. 

전세계 대상으로 하는 cdn 서비스은 CloudFront를 이용합니다. 

이작업들을 안전하게 진행하려면 클라이언트는 글로벌 배포를 위한 cloudFront와 인터랙트합니다. 오리진 액세스를 제한해서 cloudFront로만 s3 버킷에 접근할 수 있도록 합니다. 

버킷 저책을 추가합니다. RESt api는 REST HTTPS 로 Amazon API GAteway와 통신하고 게이트웨이는 람다 함수를 호출하고 람다 함수는 dynamo db에 쿼리를 날리고 읽기가 많이 발생하니 dax를 캐시 레이어로 쓰면 훌륭합니다. 

글로벌로 서빙할때 지역에 따라 지연시간을 줄이기 위해 dynamo db 의 글로벌 db를 사용하는 것도 좋겠습니다. 

메일을 보내기 위해서는 dynamo DB 스트림을 이용하고 스트림은 람다 함수를 호출합니다. 

이 람다 함수는 iam 역할을 부여 받아 ses 를 사용할 수 있게 해줍니다. 

람다함수는 sdk 를 이용해 ses가 이메일을 발송하게 합니다. 

이미지를 업로드하고 섬네일을 생성한다는 부분에서 클라이언트에서 s3 버킷으로 바로 업로드 할수 있고 cloudfornt oal를 이용가능합니다. 이 방식은 사진을 cloudFront에 업로드하고 cloudFront 사진을 s3 버킷을 전달합니다
-> S3 Transfer Acceleration 이라고 합니다. 

데이터를 글로벌로
서빙하기 위해 Dynamo DB를 붙였습니다
오로라 글로벌 데이터베이스를 사용할 수도 있었지만 이 사례가 서버리스가 아닌
프로비저닝 오로라를 사용했어야 했을 겁니다

Dynamo DB 스트림을 활용할 수 있었고요
사용자 테이블에 발생하는 변경사항을 알려주는 용도죠, 람다 함수 호출 용도도 있고요

S3는 이벤트 알림을 위해 SQS, SNS, 람다를 호출할 수 있는 것도 다뤘습니다.

# Microsoft

많은 서비스가 REST API를 통해 상호작용합니다. 

서비스 개발 수명 주기를 줄이고자 함입니다. 각 서비스가 독립적으로 확장하며 코드 리포지토리를 갖추길 원한다면 사용하면 됩니다. 

동기식 패턴은 다른 마이크로 서비스를 명시적으로 호출합니다.
다른 마이크로서비스에 HTTPS 호출을 보내기에 적합해요

비동기식 패턴도 있습니다
도면을 준비하진 못했지만
S3처럼 SQS, Kinesis, SNS Lambda에 트리거로 작용하는 경우죠

마이크로서비스의 문제는
새로운 마이크로서비스를 생성할 때마다
오버헤드가 발생한다는 점입니다

하지만 이런 문제는
서버리스 패턴으로 어느 정도 해결된답니다
API Gateway나 Lambda는 자동으로 확장해요
사용량만큼 돈만 내면 되니까
서버 사용률을 걱정할 필요가 없어집니다

API Gateway를 위한 Swagger 통합으로
클라이언트 SDK 생성이 가능하고요

# Softaware updates offloading

많이들 업데이트를 원하니까요 그래서 콘텐츠는 네트워킹을 통해
다수에게 배포되는데 비용이 많이 듭니다
애플리케이션을 변경하거나 아키텍팅을 다시 하는 일 없이
비용과 CPU를 최적화하고 싶을 때


애플리케이션을 전 세계로 확장하고
CPU 사용률을 줄이면서
비용을 절감하는 방법은 사실 아주 쉽습니다
CloudFront를 상위에 두면 됩니다

CloudFront가 기존 애플리케이션의 확장성을 높이고
비용을 절감하는 데 용이하다는 점을 기억해 두세요
엣지에서 캐싱을 활용하는 정적 콘텐츠가
대부분인 경우에 말이죠

# quiz

Question 5:
DynamoDB Streams을 사용해 DynamoDB 테이블로 항목이 추가될 때마다 Lambda 함수가 각 아이템을 실행하도록 구성한 상황입니다. 이 함수는 추후 장기적인 처리 업무를 위해 메시지를 SQS 대기열로 삽입하게 되어 있습니다. Lambda 함수를 매번 호출해보면 DynamoDB Stream을 읽을 수는 있으나 SQS 대기열로 메시지를 삽입하지는 못하는 것으로 보입니다. 이 경우, 무엇이 문제일까요?

Answer : Lambda 실행 IAM 역할에 권한이 없음 
Wrong : sqs의 대기열과 보안그룹은 연결되지 않는다. 


Question 8:
이 AWS 서비스를 사용하면 실시간으로 초당 기가바이트의 데이터를 포착할 수 있고, 리플레이 기능을 통해 이러한 데이터를 여러 개의 소비 애플리케이션으로 전송할 수 있습니다.

Answer : Amazon Kinesis Data Streams(KDS)은 확장성과 내구성이 뛰어난 실시간 데이터 스트리밍 서비스입니다. 이는 웹사이트, 클릭스트림, 데이터베이스 이벤트 스트림, 금융 거래, 소셜 미디어 피드, IT 로그 및 위치 추적 이벤트 등의 수백 개 소스로부터 초당 GB에 달하는 데이터를 지속적으로 포착할 수 있습니다.