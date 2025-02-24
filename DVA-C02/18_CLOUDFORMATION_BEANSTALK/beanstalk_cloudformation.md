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

- 마지막으로 트래픽 분할 옵션은 카나리아 테스트용 옵션입니다 트래픽의 일부를 완전히 새로운 배포로 전송하여 수행합니다
