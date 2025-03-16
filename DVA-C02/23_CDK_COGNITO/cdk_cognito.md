# cloud development kit(cdk)
- 이걸 쓰면 JavaScript, TypeScript, Python, Java, .NET 등 여러분에게 익숙한 프로그래밍 언어를 써서 클라우드 인프라를 정의할 수 있어요
- 이건 CloudFormation을 대체하는 것이에요
- 즉 우린 인프라와 애플리케이션 런타임 코드를 함께 배포할 수 있다는 말도 되죠 혁명적인 것이죠, 왜냐면 CloudFormation 템플릿은 YAML이고 타입이 유지되지 않으니까요
- SAM의 초점은 Lambda 함수와 서버리스 애플리케이션에 있어요
- 반면에 CDK는 CloudFormation의 상위집합이라고 할 수 있고요
  - 그래서 지금 나와 있는 모든 AWS 서비스를 지원해요, 그리고 TypeScript, Python, Java, .NET 같이 여러분에게 익숙한 프로그래밍 언어를 써서 인프라를 작성할 수 있어요
  - ![image](https://github.com/user-attachments/assets/50cca2e6-51f7-45e6-bbd5-53fd0b9f6c57)

# cdk constructs
- CDK가 최종 CloudFormation 스택을 생성하는 데 필요한 모든 것들을 담아놓은 구성 요소입니다
- 1계층의 경우 CFN 리소스라고 하는데요, CloudFormation 내의 사용 가능한 모든 리소스를 표현하기 때문입니다
- 계층 2는 AWS 리소스인데 더 높은 수준의 리소스예요, 지금 intent를 살펴보고 있는데 intent는 더 높은 수준의 컨스트럭트이기 때문입니다
- 다음은 계층 3 컨스트럭트인데요, 여러 관련 리소스를 나타내기 때문에 패턴이라고 합니다 여기 Lambda REST API가 있고 리소스 추가하기, 다시 리소스 추가하기, HTTP 통합 등이 있습니다
- CDK로는 빈 칸만 채우면 자동으로 ALB가 생성되고 Fargate 서비스를 올바른 포트에, 올바른보안 그룹 그리고 올바른 리스너 등과 연결해줍니다

# important commands to know
- 첫 번째는 CDK CLI와 라이브러리를 설치하는 명령어로, 바로 CDK 스택을 작성할 수 있죠
- 두 번째 cdk init app은 앱을 특정 템플릿에서 초기화하는데요, Python, JavaScript 등을 선택할 수
- 부트스트래핑은 CDK 앱을 AWS 환경에 배포할 수 있게 미리 CDK에 맞게 리소스를 프로비저닝하는 과정입니다
  - CDK에서 보면 환경은 계정과 리전을 결합한 겁니다
  - aws://<aws_account>/<aws_region>을 입력합니다 그러면 이렇게 됩니다
  - ![image](https://github.com/user-attachments/assets/d33b90b4-6217-4d5a-96c0-6d77adaea975)

 # cdk unit testing
 - Python 코드나 JavaScript 등을 테스트할 때와 마찬가지 방법으로 코드를 테스트할 수 있습니다
 - 이 어서션 모듈을 이용해 필요한 특정 리소스나 역할, 조건, 파라미터 등이 있는지 확인할 수 있습니다
- 첫 번째는 Fine-grained Assertion이라고 하는 건데요, 가장 흔한 방식이에요, 특정 리소스가 특정 프로퍼티를 갖추고 있는지를 테스트하게 되죠
- 다른 방법으로는 템플릿이 CDK에 아직 없을 경우 Template.fromString(MyString)을 입력해서 가져오기할 수 있어요
  - CloudFormation 템플릿이 CDK 외부에 있어도 외부의 CloudFormation 템플릿을 기준으로 테스트를 실행할 수 있습니다. 

# cognito
- 목표는 사용자에게 자격 증명을 부여해서 웹, 모바일 애플리케이션과 상호작용하게 하는 거예요
- 사용자는 보통AWS 계정 외부에 자리하고 있어서 이름이 Cognito인데요, 우리가 아직 모르는 사용자에게 자격 증명을 부여하기 때문이죠
- Cognito 사용자 풀은 앱 사용자를 위해 로그인 기능을 제공하는데요, API Gateway 그리고 애플리케이션 로드 밸런서와 통합이 정말 잘 되어 있죠
- Cognito 자격 증명 풀이 있는데 페더레이션 자격 증명이라는 명칭이 익숙하죠, 이건 우리 앱에 등록된 사용자가 AWS의 리소스에 바로 액세스할 수 있도록 임시 AWS 자격 증명을 제공하는 겁니다
  - 그리고 앞으로 보게 되겠지만 Cognito 사용자 풀과도 통합이 잘 됩니다
- “수백만 사용자”나 “모바일 사용자”, “SAML 인증” 등등 키워드로

# cognito user pools 
- 웹 및 모바일 애플리케이션 사용자들의 서버리스 데이터베이스를 생성할 수 있는 방법이죠
- 그럼 서버리스 데이터베이스란 뭘까요? 이 말은 사용자들의 단순 로그인, saml, google, facebook
- 만약 개인 정보가 다른 곳에서 유출된 경우에 사용자를 차단할 수 있는 기능도 있습니다
- 로그인을 하면 API로부터 JWT 즉 JSON 웹 토큰을 받게 됩니다
- OpenID Connect를 통해 더 특수한 신분 제공자와도 통합 가능해요
- 애플리케이션 로드 밸런서 그리고 리스너와 규칙을 사용해서 Cognito 사용자 풀에 대해 사용자들을 인증할 수 있고 인증이 끝나고 나면 사용자들을 대상 그룹의 백엔드로 보낼 수 있습니다
- ![image](https://github.com/user-attachments/assets/5f21004a-6d34-433a-9cbf-b72321cbe585)

# cognito user pools - ex
- 먼저Lambda트리거를 사용해서 Cognito 사용자 풀이 이 트리거 발생 시 Lambda 함수를 동시에 호출할 수 있습니다
- 전 인증과 사후 인증, 사전 토큰 생성인데요, Lambda 트리거로
- Cognito 사용자 풀에서 또 한 가지 좋은 점은 호스팅 인증 UI가 있다는 건데요, 이걸 사용하면 애플리케이션에 해당 UI를 프로그래밍하지 않아도 되죠
  - 이 호스팅 UI를 사용하면 SNS 로그인, OIDC 또는 SAML과 전부 통합해주는 기반이 만들어져서 하나하나 다시 프로그래밍할 필요가 없죠
- 이때 알아둬야 할 요령이 Cognito 사용자 풀에 사용자 지정 도메인을 사용하게 되면 그게 어디에 생성되든 상관없이 HTTPS 사용에 필요한 인증서를 생성해야 한다는 거예요
- 리전은 us-east-1으로 선택해야 합니다 -> acm 인증서 생성
- 조정 인증 메카니즘이 수행하는 작업은 로그인 시도나 위험 점수 시도 실패 등 종류와 상관없이 CloudWatch Logs에 기록됩니다
- 이 sub UUID를 사용하면 예를 들어 Cognito 데이터베이스에 저장해놓은 사용자에 관한 어떤 정보든 원하는 정보를 복구할 수 있습니다
- 다시 말하지만 추가 정보가 필요하다면 Cognito 사용자 풀 데이터베이스에서 사용자 ID와 sub UUID를 이용해 해당 사용자를 찾아보세요

# alb - authenticate users 
- 우리는 Cognito를 API Gateway와 통합해서 사용자를 인증할 수 있다고 알고 있죠,
- 동일한 작업을 애플리케이션 로드 밸런서(ALB)로도 할 수 있습니다
- 첫 번째 방법은OpenID Connect,즉 OIDC를 준수하는 자격 증명 공급자를 사용하는 겁니다
- 두 번째 옵션은 Cognito 사용자 풀을 사용하는 거예요, Amazon 로그인이나 Facebook 로그인 Google 로그인 같은 소셜 자격 증명 공급자용이죠
- 아니면 SAML, LDAP, Microsoft AD와 호환되는 기업 자격 증명이 있는 경우입니다
  - 첫 번째 옵션은 Cognito 사용자 풀 없이 직접 통합하는 거고요, 두 번째 옵션은
    - ![image](https://github.com/user-attachments/assets/3fdcf89d-f355-4883-bde9-a9431f0d45b1)

  - Cognito 사용자 풀을 사용하는 건데요,
    - ![image](https://github.com/user-attachments/assets/c7d07a8d-675b-42b0-ab7c-424ceb33ff6e)

# cognito identity pools 
- 사용자들은 AWS 환경의 외부에 존재합니다
- 예를 들어 DynamoDB 테이블이나 S3 버킷 등으로 액세스를 하려 하는 거죠 그리고 이런 액세스를 위해서는 임시 AWS 자격 증명이 필요합니다
- Amazon Cognito 사용자 풀로 이미 로그인된 사용자들을 허용하거나 OpenID Connect나 SAML 제공자도 허용할 수 있으며
- 커스텀 로그인 서버인 Developer Authenticated Identities도 가능하죠
- 게스트 사용자들에게 AWS 자격 증명을 주는 게 가능합니다
- Cognito 자격 증명 풀에서의 설정을 기반으로 한 IAM 정책을 가지고 있습니다
-  IAM 사용자를 생성하고 싶지는 않다면 Cognito 자격 증명 풀을 사용하여 그전에 토큰을 획득하고 saml, google .. open idconnect 로 연결합니다.
-  임시 자격 증명을 받기 위해 STS 서비스와 통신할 겁니다
-  이 과정이 끝나면 자격 증명은 애플리케이션으로 반환되고 이러한 자격 증명과 관련 IAM 정책으로 인해 사용자들은 AWS를 사용하고 또 AWS에 직접 액세스할 수 있게 됩니다
  - ![image](https://github.com/user-attachments/assets/efadde31-872c-4c1b-90a0-1434483aa89e)
- 그렇다면 Cognito 자격 증명 풀과 Cognito 사용자 풀을 함께 사용
- Cognito 사용자 풀로부터 획득한 JSON 웹 토큰을 Cognito 자격 증명 풀에서 자격 증명으로 교환을 하면 이 토큰에 대한 검증을 거친 뒤 STS 서비스로 통신을 해 자격 증명을 받고 그 자격 증명은 웹 및 모바일 애플리케이션으로 반환될 거고요
  - ![image](https://github.com/user-attachments/assets/78c7a73d-0c6c-4376-9d1a-8766aaff43dc)

- 즉 한 게스트 사용자가 한 IAM 역할을 가지면 다른 사용자는 다른 IAM 역할을 가지는 식이죠
- 앞선 슬라이드에서 설명했듯 이런 IAM 자격 증명은 STS를 통해서 Cognito 자격 증명 풀에 의해 획득됩니다

# cognito user pools vs identity pools
- Cognito 사용자 풀은 인증, 즉 자격 증명 인증에 사용됩니다 웹이나 모바일 애플리케이션 사용자를 모아놓은 데이터베이스가 되는 거죠
  - SAML을 이용한 기업 로그인도 가능합니다
  - 로고를 비롯해 인증에 필요한 호스팅 UI를 사용자 지정할 수도 있고요, 인증 흐름에서
  - 사전 또는 사후 등의 방식으로 Lambda와 통합되기도 합니다
  - 예를 들어 조정 인증으로 다중 인증을 알맞은 때에 사용할 수 있습니다

- Cognito 자격 증명 풀은 권한 부여, 즉 액세스 관리에 쓰이는데 AWS안에서 액세스 관리가 이루어집니다
  - 하지만 이 사용자들이 AWS 환경, 예를 들어 DynamoDB나 S3 버킷에 액세스할 수 있게 하려면 권한 부여를 해줘야 합니다
  - 여기서 사용자용 임시 자격 증명을 얻고, 자격 증명을 얻기 위한 로그인은 이 자격 증명용 토큰으로 교환하는 방식입니다
---
- Cognito 자격 증명 풀은 Cognito 사용자 풀과 함께 사용 가능하지만 자격 증명 풀 자체만 사용해도 됩니다
- 자격 증명 풀에 한번 설정이 되면 사용자들은 특정 IAM 역할이나 정책에 매핑되고
- 우리가 Cognito 자격 증명 풀을 직접 Cognito 사용자 풀과 통합하는 방식이죠 그러면 STS의 도움으로 임시 자격 증명을 얻으면 웹과 모바일 애플리케이션이 직접 AWS로 API 호출을 발급할 수 있습니다
