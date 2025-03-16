# sam
- CloudFormation 실행을 간단히 한 것이지만
- 시험에서는 SAM에 대한 기본적인 내용만 출제됩니다
- 서버리스 애플리케이션을 개발하고 배포하기 위한 실제 프레임워크입니다
- 즉, 코드를 작성한 다음 SAM 프레임워크와 호환되는 YAML 형식의 구성 파일을 사용하게 됩니다
- 그리고 자동으로 SAM은 간단한 SAM YAML 파일에서 매우 복잡한 CloudFormation 파일을 생성합니다
- SAM은 내부적으로 CodeDeploy를 사용하여 람다 함수를 배포할 수 있으며 또한 람다, API 게이트웨이, 다이나모DB를 로컬에서 실행하는 데 도움을 줄 수 있습니다
- SAM은 레시피로 구성되며 템플릿 맨 위에 SAM 템플릿임을 나타내는 변환 헤더를 추가하게 됩니다
- 그런 다음 코드를 작성하는데 CloudFormation 구성을 사용하는 대신 SAM Construct을 사용합니다
  - 예를 들어, 람다 함수인 서버리스 함수나 API 게이트웨이가 될 서버리스 API, 다이나모DB가 될 서버리스 SimpleTable이 있습니다
- 그런 다음 이 애플리케이션을 AWS에 패키지화하여 배포하려면 동일한 배포 명령을 사용해야 합니다
  - 이제 sam deploy를 자체적으로 수행할 수 있으며 그것이 package 기능을 수행합니다
  - 또한 SAM Accelerate를 사용하여 AWS 람다에 변경 사항을 매우 빠르게 동기화하는 방법도 있습니다 명령어는 sam sync --watch입니다
    - SAM Accelerate는 AWS에 리소스를 배포하는 동안 지연 시간을 줄이는 데 사용되는 일련의 기능입니다
    - 따라서 클라우드에서 람다 기능을 테스트하고 싶다면 매우 쉽고 빠르게 할 수 있습니다
    - sam sync를 실행하여 코드와 인프라를 동기화하거나
    - 따라서 CloudFormation을 사용하지 않을 것입니다
    - 백그라운드에서 실행되며 변경 사항이 감지되면 자동으로 동기화됩니다
- ![image](https://github.com/user-attachments/assets/ae778171-7309-46db-86ae-47befb63822a)

# sam multiple environment
- 물론 필요한 만큼 많은 환경을 정의할 수 있습니다
- ![image](https://github.com/user-attachments/assets/93127b30-bf8f-459a-af4b-52617b617d65)
