# step fucntions workflow
- Step Functions는 워크플로우를 상태 머신으로 모델링 하게 해줍니다
- 워크플로우의 각 단계는, 예를 들어, 람다 함수나 DynamoDB로 데이터 삽입을 하거나 특정 ECS 태스크일 때 Step Functions가 워크플로우를 조율해 줄 것입니다
- 워크플로우를 시작하기 위해 SDK API 호출을 사용할 수 있고 API Gateway 또는 CloudWatch 이벤트, Amazon EventBridge를 이용할 수 있습니다
- ![image](https://github.com/user-attachments/assets/4ab450fc-73f9-4f72-b779-403c4a23a62a)
- 병렬 상태는 브랜치의 수행을 병렬적으로 수행합니다
- Step Functions는 여러분이 해야 할 작업을 조율하고 상태 머신이 어떻게 되어야 하는지, 워크플로우의 모습을 정의해 줍니다
- 람다 함수를 호출하기 위해 태스크 상태를 정의하고자 한다면 람다 함수에 페이로드를 입력값으로 전달합니다 그리고 다음 상태로 넘어가고 시간초과로 넘어가서 람다 함수의 시간이 초과하였는지 확인합니다

# error handling in step functions
- Step functions는 많은 작은 태스크를 실행합니다 - api 통신
- 상태 머신에 정의 문제가 있을 수 있습니다 람다 함수 자체를 캐치해서는 안 되고 Step functions의 오류 처리 메커니즘을 캐치해야 합니다
- 또는 네트워크 파티션 이벤트와 같은 일시적인 실패가 있습니다 태스크를 재시도하는 것과 실패한 경로로 전환하는 캐치가 있습니다
- States.ALL은 어떤 오류 이름과도 매칭하고 State.Timeout은 태스크가 시간초과 초보다 길게 실행하거나 활동으로부터 아무 신호도 받지 못했을 때입니다
- TaskFailed는 태스크의 실행이 자체적으로 실패할 때입니다
- States.Permissions는 어떤 코드를 실행할 권한이 충분하지 않는 경우입니다
- 람다 함수 외부인 Step functions에서 정의하므로 재시도와 오류 처리 논리를 JSON 문서 내에서 직접 수정할 수 있습니다
- 다음으로 모든 재시도가 끝나면 캐치(Catch)로 갑니다
- ResultPath는 다음 필드로 지정된 상태로 전송되는 입력값의 경로를 결정합니다
  - ResultPath는 입력값으로부터의 오류를 다음 태스크의 결괏값으로 어떻게 전달할 것인지를 나타냅니다
 
# wait for task token
- 기본적으로 Step Functions와 실행 워크플로가 정의된 다음에는 waitForTaskToken이 반환되기를 기다려야 하죠, 외부 시스템의 응답(사용자의 승인, 서드파티 통합, 레거시 시스템 호출 등을)을 기다려야 하니까요
- 이 방식의 장점은 Step Functions 워크플로로 돌아가기 전에 외부 시스템을 통한 처리 작업을 수행할 수 있다는 거예요
- ![image](https://github.com/user-attachments/assets/c890fb4e-877d-456b-a8df-c3349e86f755)

# activity tasks
- 액티비티 워커란 Step Functions에서 작업을 수행하는 구성 요소로 EC2 인스턴스, 람다 함수, 모바일 기기 등 원하는 모든 곳에서 실행할 수 있어요
- 이전과 동일하게 SendTaskSuccess와 SendTaskFailure API를 사용하여 응답을 보냅니다
- EC2 인스턴스나 그 밖의 애플리케이션들이 Step Functions에서 특정 작업을 폴링하므로 EC2 인스턴스가 Step Functions에 연결할 수만 있으면 되기 때문에 네트워크가 매우 쉽습니다
- 이처럼 액티비티 태스크에 대한 폴링 기반 메커니즘과 waitForTaskToken이 포함된 콜백 패턴에 대한 푸시 기반 메커니즘이라는 상당히 다른 작업 방식을 가지고 있습니다
- ![image](https://github.com/user-attachments/assets/05020eed-0dd8-4816-a33e-e8ed17cd5c06)
- HeartbeatSeconds는 Step Functions에 구성하여 하트비트에 대한 최대 대기 시간을 설정할 수 있는데요 최대 1년까지 대기할 수 있습니다

# stadard vs express
- 첫 번째는 기본값인 표준 워크플로를 사용하는 것이고
  - 워크플로를 정확히 1회 실행하는 모델로, 초당 약 2,000개의 표준 워크플로를 실행할 수 있는데
  - 더 많은 로그를 보거나 로그 보존 설정을 통해 로그를 영구 보관할 수도 있어요
  - 멱등성이 없는 작업에 사용됩니다
- 두 번째는 익스프레스 워크플로를 사용하는 건데 익스프레스 워크플로에는 비동기식 워크플로와 동기식 익스프레스 워크플로가
  - 다음으로 익스프레스 워크플로는 이름에서 알 수 있듯이 최대 실행 시간이 5분인
  - 초당 10만 회 이상 실행을 통해 상당히 많은 양의 작업을
  - Cloudwatch 로그를 통해서만 실행 결과와 분석 자료를 얻을 수 있습니다
  - 익스프레스 워크플로는 IoT 데이터 수집이나 스트리밍 데이터 처리 또는 모바일 앱 백엔드 등에 사용됩니다
  - 가장 큰 차이점은 비동기식 익스프레스 워크플로는 최소 1회 실행 보장 모델이고 동기식은 익스프레스 워크플로는 최대 1회 실행 모델이라는 것인데, 시험에 나올 수 있으니
  - 동기식 익스프레스의 경우 마이크로서비스를 오케스트레이션하고 실행을 중지하기 전에 모든 것이 제대로 작동하는지 확인하고자 할 경우, 동기식 워크플로를 사용하면 API 게이트웨이나 람다 함수에서 호출하여 응답을 얻을 수 있어요

# appsync
- AppSync는 시험에서 두 가지 포인트가 있습니다 첫 번째는 GraphQL을 사용하는 관리 서비스입니다
- AWS AppSync 사용의 두 번째 포인트는 실시간 WebSocket 또는 WebSockets의 MQTT 통합입니다
- AppSync는 전에 본 Cognito Sync라는 오래된 것을 대체하는 서비스입니다
- 개략적으로 AppSync는 웹 애플리케이션, GraphQL을 사용하는 모바일 애플리케이션 및 실시간 애플리케이션을 통합합니다
- ![image](https://github.com/user-attachments/assets/db6e9d8c-ab26-4457-9e05-49148fae5657)
- 첫 번째는 API_KEY로 API Gateway처럼 키를 생성하고 사용자에게 줍니다
- 두 번째는 AWS_IAM을 이용하여 IAM 사용자, 역할, 또는 계정에 걸친 접근을 부재 API에 허용하는 것입니다
- OPENID_CONNECT는 OpenID Connect 공급자와 JSON 웹 토큰을 통합합니다
- 마지막으로 AMAZON_COGNITO_USER_POOL은 Cognito 사용자 풀을 통해 생성한 기존 사용자 풀과 통합합니다
- 마지막으로 AppSync에서 사용자 정의 도메인과 HTTPS 보안을 원한다면 AppSync 앞에 CloudFront를 사용할 것을 추천합니다

# aws amplify
- Amplify는 모바일과 웹 애플리케이션을 만들 수 있는 서비스로
- ![image](https://github.com/user-attachments/assets/88c84855-bae0-4ea0-af51-2941e06d6de6)
- 첫 번째는 별도의 설치 없이 즉시 사용 가능한 인증 기능을 제공한다는 건데요
- 두 번째는 데이터스토어 관련 기능으로 amplify add api 명령어를 사용하여
- 마지막으로 Amplify Hosting은 애플리케이션을 배포하고자 할 때 사용하는 서비스로 amplify add hosting 명령어를 사용하면 최신 웹 앱을 빌드하고 호스팅할 수 있고 빌드, 테스트, 배포와 같은 CI/CD 프로세스를 수행하며
- Amplify에는 선택적으로 테스트를 실행할 수 있으며 단위 테스트와 종단간 테스트라는
- 요약하자면 앱을 빌드하는 단계에서는 단위 테스트를 실시하여
- 앱을 배포할 때는 E2E 테스트를 실시하는데
