# cicd
- 이제부터 코드를 저장하기 위한 CodeCommit와 코드에서 Beanstalk 등의 플랫폼까지 이르는 파이프라인을 자동화하는 Codepipeline을 알아봅니다
- 리포지토리로는 AWS의 타사 서비스인 GitHub AWS 서비스인 CodeCommit 또 다른 타사 서비스인 Bitbucket가 있죠
- codestart -> CodeCommit, CodeBuild, CodePipeline, CodeDeploy를 하나로 다시 그룹화하는 도구입니다
- CodeDeploy가 EC2 인스턴스 온프레미스 서버 람다 함수 역할, ECS 등을 검색하고 이들을 직접 배포합니다
- ![image](https://github.com/user-attachments/assets/d81afb33-8390-410e-87ad-b3a0c690c9d8)

# codecommit
- 버전 관리라는 개념 - 레포지토리의 사용
- 하지만 AWS의 CodeCommit을 사용하면 여러분의 코드가 AWS 클라우드의 개인 VPC 내에 있기 때문에 개인 Git 리포지토리가 생기는 것과 마찬가지입니다
- IAM을 이용한 액세스 제어가 가능하죠
- Jenkins, CodeBuild 또는 기타 CI 도구 등 업계 표준 도구와 잘 통합됩니다
- IAM 정책을 이용해서 사용자와 특정 리포지토리에 대한 역할 권한을 관리할 수 있습니다
  - IAM 사용자와 역할을 지정할 수 있죠 호스팅 면에 있어서요
- SSH 키 또는 자격 증명을 다른 사용자와 공유해서는 안 됩니다
- CodeCommit과 GitHub는 모두 Pull Requests라는 코드 리뷰를 지원합니다 모두 CodeBuild와 통합되고 SSH 및 HTTPS 인증을 제공합니다
- 코드 커밋이란 코드 저장소에 코드를 저장하는 것이라는 점을 아는 것이 좋습니다
- 외부 Git 솔루션으로 마이그레이션할 것을 추천하고 있습니다

# codepipiline
- CodePipeline은 AWS 안에서 여러분의 CI/CD를 오케스트레이션할 수 있게 해주는 시각적 워크플로 툴이에요
- CodePipeline으로 다룰 수 있고요, 호출 단계에서는 Lambda 함수나 Step 함수를 호출할 수 있죠
- 빌드, 테스트를 하고 스테이징 배포를 하고 로드 테스팅을 해서 스테이징이 잘 되는지 확인할 수 있고요
- 각각의 파이프라인이 아티팩트를 생성할 수 있어요,
- 실제로 CodePipeline이 Amazon S3를 통해 그 코드를 CodeBuild에 푸시할 거예요
- ![image](https://github.com/user-attachments/assets/86bd8ec0-8d18-4399-b43c-551637bcf3df)
- CodePipeline의 IAM 서비스 역할을 확인해서 적절한 IAM 권한을 갖고 있는지 확인할 수 있어요

# codebuild
- CodeBuild는 소스 코드를 얻는 법으로 CodeCommit, Amazon S3, Bitbucket, GitHub 등이 있는데 소스안에 빌드 지침이 있습니다
- 모범 사례는 buildspec.yml을 이용하는 것으로 시험에서는 이를 테스트할 것입니다
- 출력 로그는 나중의 분석을 위해 Amazon S3와 CloudWatch Logs에 저장될 수 있습니다
- ![image](https://github.com/user-attachments/assets/ae0a6507-4bf4-4036-8bf3-4619cf032933)
- 환경은 buildspec.yml의 실행을 위한 환경 변수를 정의할 수 있게 해주는데 plaintext(플레인텍스트)인 변수를 가질 수 있고 혹은 SSM Parameter Store에서 직접 가져올 수도 있고 AWS Secrets Manager에서 secret을 직접 가져올 수도 있습니다
- dependencies

# codedeploy
- 이름에서 알 수 있듯이, 이건 애플리케이션 배포를 자동화해주는 배포 서비스예요
- 버전들은 EC2 인스턴스나 온프레미스 서버, 혹은 Lambda 함수나 ECS 서비스에 배포할 수 있어요
- 롤백도 가능합니다.
- 마지막으로 배포 방식을 통제하기 위해 appspec.yml이라는 파일이 있어요 해당 파일을 통해 배포 방식을 정의합니다.
- 그럼 이제 EC2/On-premises 플랫폼에 대해 알아보죠
  - 하나는 현재 위치(in-place) 배포고, 다른 하나는 블루/그린 배포예요, 우린 나중에 둘 다 살펴보고
  - 우린 먼저 CodeDeploy 에이전트를 타깃 인스턴스에 배포해야 해요
  - half at a time, blue/green deployment(alb, auto scaling, 오래된것은 없어지고 새로운것은 생긴다.)
  - 에이전트는 전제조건으로서 EC2 인스턴스에 설치되어야 하고요
  - Systems Manager를 이용해서 CodeDeploy 에이전트를 자동으로 설치할 수 있어요
  - 예, 업데이트된 애플리케이션이 Amazon S3에 저장되기 때문이죠
- 그럼 이제 Lambda 플랫폼으로 CodeDeploy를 어떻게 사용하는지 알아보죠
  - ![image](https://github.com/user-attachments/assets/558d9bca-530c-4b04-9e95-1d4683548ec0)
- ecs platform
  - 그리고 그건 블루/그린 배포에만 사용할 수 있어요
    - ![image](https://github.com/user-attachments/assets/255dbc73-e5f5-47fa-b3a8-c73ad9cb03bc)

  - Canary 방식은 역시 새로운 타깃 그룹에 적은 양의 트래픽을 테스트하고 그 다음에 갑자기 모든 트래픽을 새로운 타깃 그룹으로 전환하는 방식이에요

# codedeploy deployment to ec2
- EC2 인스턴스 플릿을 인 플레이스 업데이트한 후 후크를 사용할 수도 있습니다 appspec.yml에 심어서 각 단계의 마지막에 배포를 확인하는 거죠
- deploy to an asg
  - 인 플레이스와 블루/그린의 두 가지 배포 방식이 있죠
  - blue/green 은 elb 를 사용함
- 마지막으로, 재배포와 롤백입니다
  - 배포에 실패했을 때 혹은 CloudWatch 알람이 트리거 돼서 배포에 실패했다고 알려주는 경우죠
  - 롤백을 하면 CodeDeploy는 마지막으로 감지한 성공한 리비전을 새로 배포하는데 이전으로 돌아가진 않습니다
  - 그러니 복원된 버전이 아니고 새로운 배포에 해당하죠
 
# codeartifact
- 개념은 소프트웨어를 빌드할 때 자체 소프트웨어가 종속되는 다른 소프트웨어를 사용한다는 것입니다
- 그래서 CodeArtifact를 사용하면 소프트웨어 개발을 위한 안전하고 확장 가능하며 비용 효과적인 아티팩트 관리 시스템
- ![image](https://github.com/user-attachments/assets/dc605688-d361-4a93-9a8c-b1bf08bcd28f)
  - JavaScript 패키지의 종속성을 CodeArtifact로 가져오라고 말할 수 있다는 거죠
- 다음은 CodeArtifact 변경사항이 AWS의 다운스트림에서 일부 항목을 트리거하는 방식입니다
- ![image](https://github.com/user-attachments/assets/ef4c684e-1a9a-4413-b7e0-6b6d1ca46784)
  - 이는 빌드 내 코드에 항상 최신 종속성이 있도록 보장하는 완전히 자동화된 파이프라인을 빌드하기 위한
- CodeArtifact 리포지토리 액세스 권한을 누군가에게 부여할 때는 모든 패키지 액세스 권한을 부여하거나 아무 권한도 부여하지 않아야 합니다
- 내 계정에 대한 액세스 권한을 부여하려면 리소스 정책을 사용하여 다른 계정의 밥에게 CodeArtifact 리포지토리의 패키지 읽기 권한을 부여하는데요, 이는 리소스 정책이 없으면 할 수 없습니다

# codeguru
- 머신 러닝 기반의 서비스로 두 가지 기능이 있습니다
- 자동화된 코드 검토 + 애플리케이션 성능 권장 사항
- CodeGuru Reviewer에서 정적 코드 분석으로 자동으로 코드를 분석합니다
- CodeGuru에서 모든 줄의 코드를 확인하고 버그나 메모리 누수 또는 이전에 발생한 것을 감지해 실행 가능한 권장 사항을 제공합니다
- CodeGuru Reviewer는 커밋(commit)을 보고 언제든지 코드를 푸시하면 잘못된 코드줄을 알려줍니다
- 이는 Java와 Python을 지원하고 GitHub와 GitHub, Bitbucket, CodeCommit와 통합되어 있습니다
- CodeGuru Profiler로 모니터링할 애플리케이션에 최소한의 오버헤드가 발생합니다

# codeguru agent configuration
- CodeGuru 프로파일러는 에이전트 덕분에 작동합니다, 그리고 이 에이전트를 구성하여 프로파일러를 미세 조정할 수 있습니다
- 먼저 MaxStackDepth인데요 이것은 프로필에서 표시될 코드의 양입니다
- MemoryUsageLimitPercent로, 프로파일러가 사용할 수 있는 메모리 양이죠
- ReportingIntervalInMilliseconds는 에이전트에 프로파일링 완료에 관한 보고 빈도를
- SamplingIntervalInMilliseconds도 있는데 중요합니다 샘플을 프로파일링하는 데 사용되는 샘플링 간격입니다 -> 높을 수록 얻는 샘플링 갯수 늘어남
- 
