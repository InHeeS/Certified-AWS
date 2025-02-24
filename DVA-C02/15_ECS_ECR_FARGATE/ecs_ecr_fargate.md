# Docker
- 또 온프레미스에서 클라우드로 앱을 리프트-앤-시프트하기도 하고 msa architecture
- ![image](https://github.com/user-attachments/assets/c2149793-aba3-4503-872f-0b1ca3d27d1e)
- 첫 번째는 Amazon ECS 즉 Elastic Container Service입니다 도커 관리를 위한 Amazon의 전용 플랫폼이죠
- Amazon EKS 즉 Elastic Kubernetes Service는 Kubernetes의 관리형 버전으로 오픈 소스 프로젝트입니다
- AWS Fargate는 Amazon의 서버리스 컨테이너 플랫폼으로 ECS와 EKS 둘 다 함께 작동할 수 있습니다

# ecs launch type
- `첫번째는 ec2 시작 유형`
- ECS 클러스터에서 ECS 태스크를 실행하는 것입니다
- ecs service, cluster 가 ec2 인스턴스를 등록 후에 ecs task 시작하여 컨테이너 시작과 중지를 처리
- 이것이 EC2 시작 유형입니다, 미리 프로비저닝한 Amazon EC2 인스턴스에 도커 컨테이너가 배포되는 것입니다

- `두번째 시작 유형은 fargate 시작유형`
- Fargate 유형에서 ECS 클러스터를 사용하는 경우 ECS 태스크만 정의하고 나면 AWS에서 CPU와 RAM 요구사항을 토대로 ECS 태스크를 실행합니다
- 확장하려면 태스크 수만 간단하게 늘리면 됩니다
- EC2 시작 유형보다 관리하기가 훨씬 편해서 시험에서도 잘 출제됩니다

- ecs task of iam role
- ![image](https://github.com/user-attachments/assets/6d683acd-11a8-4933-9468-6d59771d138b)

- 로드 밸런서 통합
- 애플리케이션 로드 밸런서(ALB)를 클러스터 앞에서 실행하면 사용자들은 ALB와 함께
- 백엔드의 ECS 태스크에 직접 연결하게 됩니다
- 네트워크 로드 밸런서는 높은 스루풋이나 성능이 요구되는 활용 사례 또는 뒷부분에서 배울 AWS PrivateLink를 사용하는 경우에만 권장됩니다
- 애플리케이션 로드 밸런서는 Fargate에도 사용 가능합니다

- ecs data volumes 으로는 efs 입니다.
- 데이터 공유를 위해 ECS 태스크에 파일 시스템을 마운트하려고 합니다
- 최상의 조합은 Fargate를 이용해 서버리스 방식으로 ECS 태스크를 실행하고 Amazon EFS를 파일 시스템 지속성에 이용하는 거예요
- EFS도 서버리스 방식이어서 서버를 관리할 필요가 없고 사용량을 기반으로 요금이 청구되고 사전에 프로비저닝하면 사용 가능합니다
 
# ecs auto scaling
- ECS 서비스의 CPU 사용률을 확장할 수 있고
  - 용량 공급자 
- 메모리 사용률 즉 ECS 서비스의 RAM이나
  - 용량 공급자
- ALB 관련 지표인 타겟당 요청 수를 확장할 수 있어요
 
- 특정 타겟을 추적하는 대상 추적(Target Tracking) 스케일링이나
- 단계(Step) 스케일링
- 혹은 미리 ECS 서비스 확장을 설정하는 예약(Scheduled) 스케일링 등이 있죠

---

- EC2 시작 유형이라면 태스크 레벨에서의 ECS 서비스 확장이 EC2 인스턴스 클러스터의 확장과 다르다는 사실을 기억하세요
- EC2 시작 유형에서 실행되는 서비스라면 ECS 용량 공급자가 EC2 인스턴스를 추가해 ECS 클러스터를 확장할 겁니다

# ecs rolling updates 
- ECS 서비스를 V1에서 V2로 업데이트할 때는 한 번에 얼마나 많은 태스크가 어떤 순서로 시작되고 중단될지를 제어할 수 있습니다
  
- 그리고 최소 상태(healthy) 퍼센트를 100보다 적게 설정하는 경우에는 최소 상태 퍼센트를 넘어서기에 충분한 태스크가 있기만 하다면 오른편에 있는 태스크는 모두 종료되어도 되는 겁니다
  
- 그리고 최대 퍼센트는 서비스를 롤링 업데이트하는 데에 있어 몇 개의 새로운 v2 태스크를 생성할 수 있는지를 나타냅니다

- ![image](https://github.com/user-attachments/assets/1b1165b9-4479-41d1-bdd6-45f488efdda7)

# ecs tasks event bridge
- ![image](https://github.com/user-attachments/assets/2cac1a2b-f937-45d2-9035-815703eed048)
- ![image](https://github.com/user-attachments/assets/63ea3fd5-e590-4537-9358-85b240d882e5)
- ![image](https://github.com/user-attachments/assets/85c661a1-8fbf-4391-82c4-74c4f79c3507)

# ecs task definitions
- 태스크 정의는 ECS 서비스에게 ECS 상에서 한 개, 혹은 다수의 도커 컨테이너를 실행하는 방법을 알려줍니다
- 태스크 정의 당 하나 이상의 컨테이너를 정의할 수 있다는 점
- 태스크 정의에는 중요한 정보들이 포함되어 있는데요
이미지 이름, 컨테이너에 바인딩된 포트 EC2 상에 있는 경우에는 호스트
컨테이너에 요구되는 메모리와 CPU
환경 변수 및 네트워킹 정보
태스크 정의로 연결된 IAM 역할 및
CloudWatch와 같은 로깅 구성을 포함하고 있죠

- 시험에서 중요한 요소로는 `컨테이너 포트`, 하지만 ALB가 ECS 서비스에 연결되어 있을 때에는 이 동적 호스트 포트 매핑 기능 덕분에 올바른 포트를 찾을 수 있게 됩니다
  - 해당 이미지는 ec2 시작 유형의 경우
  - ![image](https://github.com/user-attachments/assets/20fa1334-4ea0-475e-8456-761b21f5cae0)
  
- fargate 시작 유형
- Fargate에게는 호스트가 없기 때문에 컨테이너 포트만을 정의하면 되겠죠
- 각 태스크는 Elastic Network Interface(ENI)를 통해 자체 프라이빗 IP를 가지게 되는데 각 ENI는 동일한 컨테이너 포트를 가지게 될 겁니다
- ![image](https://github.com/user-attachments/assets/d652a1ce-bd3f-4293-bec1-fcbc3d8e07d3)

- ECS 내의 IAM 역할에 대한 내용이 출제될 텐데요
  - 태스크 정의 당 IAM 역할이 할당\
  - 하지만 역할은 서비스 수준이 아닌 태스크 정의 수준에서 정의된다는 점
  - ECS 태스크에 대한 IAM 역할을 어디서 지정하냐를 물어볼 텐데 정답은 '태스크 정의 상에서' 가 되겠죠
    - 태스크 정의는 환경 변수를 가질 수 있는데 이는 여러 가지 방식으로 가능합니다
  - 데이터베이스 비밀번호와 같이 민감한 변수인 경우에는 SSM 파라미터 스토어나 암호 관리자를 통해 이러한 값들을 저장하고 ECS 태스크를 시작할 때에, ECS 태스크 정의 내로부터 이들을 참조할 수 있습니다
 
- 마지막 옵션으로는 ECS 환경 변수를 Amazon S3 버킷으로부터 직접 로드하는 방법이 있는데요
  - 이는 파일을 통한 Bulk 환경 변수 로딩이라 불립니다

- 이 컨테이너들은 몇몇 파일을 서로 공유해야 하므로 데이터 볼륨을 두 컨테이너 모두에 마운팅해 이들이 데이터를 공유
  - ![image](https://github.com/user-attachments/assets/900936d7-d784-4646-95e3-a4044a466ade)

- 여러 개의 컨테이너 사이에서 데이터를 공유하거나, 혹은 사이드 카 컨테이너가 존재하고 이런 사이드 카 컨테이너가 지표 혹은 로그를 다른 목적지로 보내기 위해서 사용되는 경우에는 공유 스토리지로부터 읽기가 가능해야 하겠죠

# ecs task placement
- 태스크 배치 전략과 태스크 제한이라는 기능이 존재합니다.
- EC2 상의 ECS에만 유효합니다
- 태스크 배치 프로세스
  - 가장 먼저 태스크 정의 내의 CPU, 메모리, 그리고 포트 조건을 만족하는 인스턴스를 식별합니다
    
---

  - binpack 은 가장 많이 채워저 있는 cpu 혹인 ram 에 태스크를 배치하려 시도합니다. 인스턴스의 수를 최소화 가능하여 비용 절감
  - 컨테이너를 채운 후에야 다음 컨테이너로 넘어가는 거죠

- 무작위 태스크 배치 전략
  - 최적의 전략은 아니지만 작동하는 전략

- 분산 (spread)
  - ECS 서비스의 고가용성을 극대화할 수 있게 됩니다

---

- 태스크가 배치되는 방식을 제한하는 배치 제한 이있습니다.
  - distinctinstance
    - 동일 인스턴스 상에 두 개의 태스크를 배치할 수 없는 겁니다
      - 모든 태스크는 t2 인스턴스 상에만배치가 될 수 있는 거죠
      - ![image](https://github.com/user-attachments/assets/616d5221-5b16-4d3b-9dd3-dab0d69f151e)
     

# ECR 
- 두가지 옵션이 존재합니다.
- 첫 번째는 계정에 한해 이미지를 비공개로 저장하는 건데
- 두번째는 블릭 저장소를 사용해 Amazon ECR Public Gallery에 게시하는 방법이 있습니다

- 이미지는 백그라운드에서 Amazon S3에 저장됩니다
- ECS 클러스터의 EC2 인스턴스에 이미지를 끌어오기 위해서는 EC2 인스턴스에 IAM 역할을 지정하면 됩니다
- ECR에 대한 모든 접근은 IAM이 보호하고 있죠
- Amazon ECR은 단순히 저장하는 리포지토리에 그치지 않고 이미지의 취약점 스캐닝, 버저닝 태그 및 수명 주기 확인을 지원합니다

---

- 가장 먼저 로그인 명령어가 있는데요 이 로그인 명령어를 통해 도커 CLI에 대한 도커(Docker) 자격 증명을 얻게 됩니다

- 여러분의 컴퓨터 상에 있는 도커 CLI가 AWS 상의 프라이빗 리포지토리로 연결되도록 허용하는 거죠
-  KMS를 통한 Amazon ECR 리포지토리의 암호화 여부를 지정할 수 있는데
-  프라이빗 리포지토리는 올바른 IAM 권한이 있을 때만 이미지 풀링을 허용한다는 겁니다

# aws copilot
- Copilot은 서비스가 아닙니다 컨테이너화된 프로덕션-레디 애플리케이션을 빌드 및 릴리즈하고 동작시키는 데 사용하는 명령줄 인터페이스 도구입니다
- ECS, VPC, ELB, ECR 등과 같은 온갖 복잡한 인프라를 Copilot이 준비해 줍니다
- CLI나 YAML 파일을 이용해 마이크로서비스 애플리케이션의 아키텍처를 형성합니다
- 결과적으로 Amazon ECS, AWS Fargate, 또는 AWS App Runner로 배포할 수 있습니다

# aws eks
- 관리형 Kubernetes 클러스터를 AWS에서 시작하기 위한 수단입니다
- 컨테이너화된 애플리케이션, 주로 Docker 애플리케이션의 자동 배포, 스케일링 및 관리를 위한 오픈 소스 시스템입니다
- 두개의 모드가 있습니다.
  - EC2 인스턴스를 선택할 수 있고 EKS 클러스터에 서버리스 컨테이너를 배포하고 싶다면 Fargate 모드를 선택할 수 있습니다
- 단지 Kubernetes API를 이용하고 싶은 경우, AWS를 이용해 Kubernetes 클러스터를 관리하고 싶은 경우가 회사가 이미 Kubernetes를 사내에서 또는 다른 클라우드에서 이미 사용하고 있거나

- 클라우드 또는 컨테이터를 마이그레이션하려는 경우에 Amazon EKS 사용은 훨씬 간단한 해결책이 될 수 있습니다
- ![image](https://github.com/user-attachments/assets/ee351279-d6ef-48b4-af62-2acf4f0d6597)

# eks node types
- 관리형 노드 그룹은 즉 ec2 인스턴스를 생성하고 관리해 줍니다.
  - EKS 서비스 자체에서 관리하는 오토 스케일링 그룹의 일부이며 온디맨드 및 스팟 인스턴스를 지원합니다
- 자체 관리형 노드
  - 더 많은 사용자 지정이나 통제를 원하는 경우에 사용합니다 이 경우 노드를 직접 생성한 다음 EKS 클러스터에 등록하고 자체 노드를 ASG의 일부로서 관리해야 합니다
  - 사전 설치된 Amazon EKS 최적화 AMI를 사용할 수 있어 시간을 절약할 수 있습니다
  - 이 모드도 역시 온디맨드 및 스팟 인스턴스를 지원합니다
  - Amazon EKS 클러스터에 데이터 볼륨을 연결할 수 있습니다 그러려면 EKS 클러스터에 StorageClass 매니페스트를 지정해야 합니다
  - 시험을 위해 살펴봐야 할 키워드로는 Amazon EKS가 지원하는 Amazon EBS Fargate와 함께 작동하는 유일한 스토리지 클래스 유형인 Amazon EFS Amazon FSx for Lustre, Amazon Fsx for NetApp ONTAP이 있습니다
