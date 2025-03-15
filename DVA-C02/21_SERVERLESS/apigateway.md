# api gateway 
- 클라이언트도 이 Lambda 함수를 호출할 수 있게 하려는 방법중 하나 입니다.
- API 게이트웨이가 훌륭하게도 Lambda 함수로 요청을 프록시합니다
- 인증과 권한 부여 목적으로 API 게이트웨이에 적용할 수 있는 보안이 많습니다
- HTTP 통합도 가능하고요, 모든 HTTP 엔드포인트를 백엔드에서 노출할 수 있어요
- 인증을 추가하거나, API를 공개적으로 배포하거나, AWS 서비스에서 속도 제한을 수행하려 할 때 이용합니다
- AWS 자격 증명에 액세스하지 않고 안전한 방식으로 Kinesis Data Streams에 데이터를 전송할 수 있게 하고 싶어요
- API 게이트웨이를 통해 어떤 AWS 서비스든 외부로 노출할 수 있게 돼요

---

- 글로벌 클라이언트에 이용됩니다 세계 어디서든 API 게이트웨이를 액세스할 수 있게 되고, 효율성 측면에서
- API 게이트웨이는 생성했던 한 리전에만 남지만 모든 CloudFront Edge 위치에서 액세스할 수 있어 효율적이죠 -> regional 
-  Private 유형의 API 게이트웨이는 VPC 내에서만 액세스 가능하며 인터페이스 VPC 엔드포인트, 즉 ENI를 API 게이트웨이 액세스를 정의하는 데 리소스 정책을 쓸 수 있어요
---
- iam 역할로 사용자 식별 가능합니다.
- 모바일 애플리케이션, 웹 애플리케이션 등 외부 사용자를 두려면 Amazon Cognito라는 걸 씁니다
- 마지막으로, Route 53에 CNAME이나 A-alias 레코드를 설정하여 도메인과 API 게이트웨이를 나타내야 해요
- Edge-Optimized 엔드포인트를 사용한다면 us-east-I에 인증서가 있어야 하고
- Regional 엔드포인트를 사용한다면 API 게이트웨이 스테이지와 동일한 리전에

# api gateway deployment stages
- staging mode
  - ![image](https://github.com/user-attachments/assets/f2588fe0-37f3-4943-9433-a8b6349f05ba)
- API를 다시 배포하지 않고도 구성 값을 변경할 수 있어서,
- Lambda 함수 ARN, HTTP 엔드포인트, 매개변수 매핑 템플릿 등스테이지 변수를 통해 아주 다양한 것을 변경할 수 있어요
- ![image](https://github.com/user-attachments/assets/fb21a5a4-114d-42e8-afbc-ab4554a01463)

# api gateway - canary deployment
- API Gateway의 변경 사항에 관해 소량의 트래픽으로 테스트하는 방법을 활성화하는 것이며 주로 프로덕션 환경에서 실행합니다
- 이를 통해 백엔드의 람다 함수 등으로 API Gateway의 새 버전을 테스트할 수 있습니다
- 카나리 단계에서 원하는 단계 변수를 재정의 할 수 있는데 이는 람다와 API Gateway로 블루/그린 배포를 실행하는 것과 같습니다

# api gateway integration types
- 첫 번째는 MOCK 통합 유형으로 백엔드로 요청을 전송하지 않고도 응답이 반환됩니다
- 개발과 테스트할 용도로 api 게이트웨이를 프로그래밍하고 구성하면서도 아직 백엔드 작업은 수행하지 않을 때 유용합니다.
- HTTP 또는 Lambda와 기타 서비스 등의 AWS 유형이 있는데 여기서 API 게이트웨이가 요청을 전달하지만 수정할 수 있어요
- 예를 들어 REST API와 API 게이트웨이를 생성하고 매핑 템플릿을 추가한 다음에
- 이 REST API 호출을 SQS 큐의 API 호출에 매핑하는데, 이때 API 게이트웨이가 만든 API 호출을 SQS 큐에서 이해할 수 있도록 변경, 이름 변경, 재정렬 등을 수행하는 거예요
- 이때 API 게이트웨이로 요청과 응답을 바꿀 수 있습니다
- ![image](https://github.com/user-attachments/assets/f9ecacbc-7285-490c-9d9d-e240c2c07993)
- 그런 다음 백엔드에서 응답을 하면 API 게이트웨이가 응답을 클라이언트에게로 다시 프록시합니다
- 매핑 템플릿으로 쿼리 문자열 매개변수의 이름을 바꾸거나, 본문 콘텐츠를 수정하거나 헤더 추가 또는 수정도 가능해요
- 마지막으로, 매핑 템플릿을 설정하려면 콘텐츠 유형을 application/json 또는 application/xml로 설정해야 돼요
- 클라이언트와 SOAP API를 통합하는 방법입니다
- ![image](https://github.com/user-attachments/assets/00eaadbc-de99-46c5-989c-187878a6821f)

# api gateway - open api spec
- 여러분은 API Gateway에 있는 기존의 API를 OpenAPI 스펙으로서 엑스포트할 수 있어요
- 이 OpenAPI 스펙은 YAML 또는 JSON 형태로 작성할 수 있고요
- 여러분은 모든 API 메서드에 대해 params-only 검증자를 적용할 수도 있고요

# api gateway caching api responses
- ttl 은 300초 이며
- ![image](https://github.com/user-attachments/assets/ded49fdd-4a3b-48dc-aeaa-af49c41a8cbf)
- Cache-Control: max-age=0이라는 헤더를 넣어 캐시를 무효화할 수 있습니다

# api gateway usage plans & api keys
- 그리고 어떤 API 키가 고객을 식별하고 그들의 접근을 계산하기 위해 이 사용량 계획에 연결될 지에 관한 사용량 계획을 생성해야 합니다
- 사용량 계획을 구성하려면 기억하셔야 합니다 우선 하나 이상의 API를 만들고 API 키가 필요한 메서드를 구성하고 API를 단계에 배포합니다
- 그리고 애플리케이션 개발자가 받을 API키를 생성하거나 import하는데 이들은 여러분의 API를 사용할 고객들입니다
- API 호출 시 할당된 API 키를 API에 대한 요청의 x-api-key 헤더에 공급해야 합니다

# logging & tracing
- CloudWatch Logs를 API Gateway와 통합하기를 사용 설정하면 API Gateway를 통과하는 요청과 응답 내용에 관한 정보를 얻게 됩니다
- 첫 번째는 CacheHitCount, 다른 하나는 CacheMissCount라고 하는 메트릭입니다
- ![image](https://github.com/user-attachments/assets/af0b7df5-8599-443f-b864-929b7e364f7b)
- Latency 자체는 API Gateway가 클라이언트로부터 요청을 받을 때부터 클라이언트로 응답을 반환할 때까지의 시간이에요
- 만약 Latency나 IntegrationLatency가 29초를 넘으면 API Gateway에서 시간 초과를 알린다는 뜻이에요
- 서버 측은 백엔드를 말하고 클라이언트 측은 API Gateway를 사용하는 클라이어언트를 말하죠
- 이 내용은 Lambda의 예약된 동시성, 전체 동시성과 함께 살펴봤었죠, 하지만 이게 API Gateway에도 적용 가능합니다

# api gateway cors
- 이 API Gateway가 동작하기 위해 preflight(사전 전달) 요청이라는 OPTIONS를 만들어야 하는데 다음과 같은 CORS 헤더들이 포함되어야 합니다
- Access-Control-Allow-Methods와 Access-Control-Allow-Headers 그리고 Access-Control-Allow-Origin이며
- apigateway 에서 cors 를 활성화 할 수 있다.

# api gateway security iam permissions
- 첫 번째는 IAM 권한을 사용해 API Gateway에 접근하는 것인데
- IAM 정책을 사용하고 이를 사용자와 역할에 연결하면 API Gateway를 호출할 수 있습니다
- 첫 번째 권한 모드가 있는데 아주 간단합니다 이는 resource policy(리소스 정책)과 결합될 수 있습니다
  - 리소스 정책의 기본 사용 예는 교차 계정 접근의 사용입니다
  - IAM Security와 결합하여 다른 계정의 사용자나 역할에 직접 API Gateway의 접근 권한을 줄 수 있습니다
  - 특정 IP 주소들을 거르거나 VPC 엔드포인트만 허용할 수 있습니다
- 두 번째 카테고리는 Cognito User Pools(사용자 풀)입니다
  - Cognito로의 연결 토큰은 자동적으로 만료되기 때문에 API Gateway는 연결하려는 사람들의 신분을 Cognito로 식별합니다
  - 권한 부여는 API Gateway 메서드 레벨에서 설정됩니다
  - Cognito 사용자 풀로 Cognito 토큰을 평가하고 토큰이 올바르다면 백엔드로의 접근을 허락합니다
- Lambda Authorizer(Lambda 권한 부여자)로
  - 헤더나 쿼리 문자열 등의 요청 기반의 파라미터들을 Lambda 권한 부여자로 전달합니다
  - 보통 서드파티 인증 시스템을 사용할 경우 사용됩니다

---
- IAM 보안은 계정에 이미 생성된 사용자와 역할이 있을 때 좋고 교차 계정 접근을 위해서는 리소스 정책을 사용해야 합니다 우리가 이미 아는 방식으로 인증과 권한 부여를 다루기 좋으며 Signature v4를 이용합니다
- 마지막으로 Cognito 사용자 풀은 우리가 직접 우리 사용자 풀을 관리하는데Cognito 섹션에서 어떻게 하는지 알아 볼 것이며 어떤 사용자 정의 코드도 작성하지 않아도 되므로 선호됩니다
- 사용자 정의 권한 보유자는 서드파티의 사용자들의 데이터베이스를 사용할 때 좋습니다 어떤 IAM 정책을 리턴하는 지를 우리가 고르기 때문에 유연하고 우리가 직접 인증을 다뤄야 하고 Lambda 함수에서 권한 부여를 다뤄야 합니다

# http api vs rest api
- HTTP API가 REST API보다 훨씬 저렴하다는 점이고요
- 예를 들어 REST API는 리소스 정책을 지원하지만 HTTP API는 그렇지 않아요

# websocket api 
- 따라서 상태 유지 애플리케이션에서 사용이 가능하죠
- ![image](https://github.com/user-attachments/assets/75c8efbb-c9a0-4a83-9f97-b6293a913804)
- connectionId는 클라이언트가 API 게이트웨이에 연결된 상태인 이상 그대로 유지되죠
- 그리고 API 게이트웨이가 있을 때는 연결 URL 콜백이 수행됩니다
- ![image](https://github.com/user-attachments/assets/f95347db-8fe7-443c-bb0c-2a8f97ea9f88)
- 라우팅 기능이 존재하여 일치하는 라우트가 없는 경우에는 기본 라우트로 전송됩니다
# architecture
- API 게이트웨이 덕분에 회사의 마이크로서비스를 모두 단일 인터페이스에서 표시할 수 있습니다
- 마이크로서비스를 통합하고 외부 통합 URL을 제공하는 것이 그 목적인 것이죠
- 라우팅 서비스와 데이터 변환 SSL 인증서 지정 등
- ![image](https://github.com/user-attachments/assets/72e3b8af-124b-436e-8038-66eca6038476)
