# elastic beanstalk
- 애플리케이션을 배포하는 단일 방법
- Elastic Beanstalk의 아이디어는 하나의 인터페이스에서 EC2, ASG, ELB, RDS와 같이 이전에 본 모든 구성 요소를 재사용
- 따라서 Elastic Beanstalk는 용량 프로비저닝, 로드 밸런서 구성, 자동 확장, 애플리케이션 안정성 모니터링과 인스턴스 구성 등을 처리
- 또한 Beanstalk에서 개발, 테스트, 프로덕션 등 여러 환경을 생성할 수 있으며 원하는 모든 환경을 만들 수 있습니다
- ![image](https://github.com/user-attachments/assets/732e2c15-2d77-49ca-8861-baaff45af274)

---

 - server tier
 - worker iter
 - ![image](https://github.com/user-attachments/assets/41427e0e-b4cb-4909-8570-0490ca9ca362)

- deployment modes
  - 개발 목적에 적합한 단일 인스턴스
  - 탄력적 ip 가진 ec2 instance
- 마스터와 스탠바이 인스턴스를 가진 Multi-az RDS 데이터베이스를 가질 수도 있습니다
- ![image](https://github.com/user-attachments/assets/940a4078-e1ff-494f-aa3a-527d5bbe992b)

# beanstalk deployment options for updates
- 첫 번째 옵션은 ‘한 번에 모두’로 한 번에 모든 인스턴스를 배포합니다, 가장 빠르지만 인스턴스가 업데이트되는 동안 서비스가 잠시 중지되어 다운타임이 발생합니다

- ‘롤링’ 옵션은 버킷이라는 인스턴스 묶음을 동시에 업데이트하고 첫 번째 버킷의 상태가 괜찮으면 다음 버킷으로 넘어갑니다
  - prod 환경을 다루는 데 유용합니다
  - 아주 약간이지만 추가 요금이 있고 시험에 추가 요금이 있는 항목을 고르는
  
- ‘변경 불가능’ 옵션은 오토 스케일링 그룹에 인스턴스가 있을 때 새 인스턴스를 다같이 배포합니다, blue/green
  - 다음으로 ‘변경 불가능’ 옵션이 있습니다, 이 배포 옵션도 역시 다운타임이 0입니다
  - 이 인스턴스는 임시 ASG에 연결됩니다 그래서 비용이 많이 들고
  - 그리고 배포 시간이 가장 긴 옵션입니다
  - 하지만 보너스로 실패 시 빠른 롤백을 제공합니다,
  - 
- 마지막으로 트래픽 분할 옵션은 카나리아 테스트용 옵션입니다 트래픽의 일부를 완전히 새로운 배포로 전송하여 수행합니다
   - 이 테스트에서는 새 애플리케이션 버전이 같은 용량의 임시 오토 스케일링 그룹에 배포됩니다
   - 그리고 배포가 실패하거나 메트릭이 잘못되면 자동화된 롤백이 트리거됩니다
   - 블루 그린 방식에서 크게 발전한 방식이기 때문입니다

- blue green
   -그리고 Route 53에서 가중치 기반 정책을 설정해 트래픽의 90%를 블루 환경으로 나머지 10% 그린 환경으로 보냅니다. 

# elastic beanstalk cli
- 이제 개발 파이프라인을 자동화하려면 EB CLI 사용이 적합합니다
- EB CLI는 일래스틱 Beanstalk에서 CLI를 사용할 때 효율성을 높이는데 도움이 됩니다
- EB CLI도 동일한 방식입니다 zip 파일을 생성하고 업로드한 뒤 배포하는 것이죠
  - 종속성 설명
- 그리고 zip 파일을 Beanstalk에 업로드하면 Amazon S3에 업로드되고Beanstalk 인터페이스에서 Amazon S3 번들을 참조합니다

# beanstalk lifecycle policy
- 시간을 기준으로 해서 오래도니 버전을 삭제하거나 공간을 기반으로 가능
- 이런 삭제를 수행하는 역할은 무엇인가요 aws-elasticbeanstalk-service-role입니다

# beanstalk extensions
- UI에서 설정한 모든 매개변수도 파일을 사용하여 코드로 구성할 수 있습니다
- YAML이나 JSON 형식이어야 하는데요
- EB 확장으로 리소스를 추가할 수도 있습니다 (rds,elasticcache, dymamodb)
- 삭제하면 확장에서 관리되는 모든것들이 연쇄적으로 삭제됨

# beanstalk under the hood
- Beanstalk은 이면에서 CloudFormation을 기반으로 합니다
- CloudFormation는 다른 AWS 서비스를 프로비저닝 하는 코드형 인프라 서비스입니다
- CloudFormation을 이용해서 일래스티 캐시, DynamoDB S3 버킷을 배포할 수 있다는 점 기억하시면 좋겠습니다

# beanstalk cloning
- 기존 환경을 새로운 환경으로 복제할 수 있어요
- 원래 환경의 모든 리소스와 설정은 그대로 보존됩니다
- 로드 밸런서 유형과 구성, 그리고 RDS 데이터베이스 타입이 있습니다

# beanstalk migration : load balancer
- 첫 번째는 로드 밸런서인데 Beanstalk 환경을 한번 생성하고 나면 일래스틱 로드 밸런서 유형은 변경이 불가능하고, 구성만 바꿀 수 있습니다
- 때문에 변경하려면 다음과 같은 작업이 필요합니다.
  - 먼저 동일한 구성으로 새로운 환경을 생성합니다 로드 밸런서만 제외하고 말이죠
  - 애플리케이션을 새 환경에 배포하는 겁니다
  - 이를 위해서 CNAME 교체나 Route 53을 이용해서 DNS 업데이트를 수행합니다
- rds
  - RDS 데이터베이스를 Beanstalk 환경에서 분리한 다음 환경 변수 등을 이용하여 이를 연결 문자열로 참조하는 겁니다
  - RDS 데이터베이스에 대한 스냅샷을 생성합니다
  - RDS 데이터베이스가 삭제되지 않도록 보호해 줍니다
  - CloudFormation으로 이동해서 해당 스택을 수동으로 삭제해야 하죠
