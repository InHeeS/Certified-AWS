# vpc
- 이 강의는 AWS Certified Developer 수준이기 때문에 그렇게까지 깊게 알 필요는 없습니다
- virtual private cloud 이며 사설 네트워크를 의미
- 리전이 2개일 대 2개의 다른 vpc 를 가집니다.
- 논리적인 구조인 VPC 내부에는 서브넷이 있고 이것이 VPC 안에 있는 네트워크를 분할하게 해줍니다
- 사설 서브넷은 언터넷에서 접근할 수 없습니다.
- 인터넷으로의 접근과 서브넷 사이의 접근을 정의하기 위해 라우팅 테이블을 사용할 것입니다
- VPC 내에서 여러 라우팅 테이블을 정의하여 접근을 허용
- 공용 서브넷이 인터넷에 접근하기 위해서는 인터넷 게이트웨이를 사용합니다.
  - 인터넷 게이트웨이가 서브넷에 있는 vpc 인스턴스가 인터넷에 연결
 
- 사설 서브넷을 위해 nat 를 제공하여 인터넷 접근을 막는다.
  - 그러나 NAT 게이트웨이는 AWS에서 관리하기 때문에 프로비저닝과 스케일링 관련해서 신경 쓸 필요가 없어요

  - 반면에 NAT 인스턴스는 자체 관리됩니다 둘 다 사설 서브넷에 있는 인스턴스가 비공개를 유지한 채 인터넷에 접근할 수 있게 해줍니다
  - NAT 게이트웨이 또는 NAT 인스턴스를 공용 서브넷에 배포하고 사설 서브넷에서 NAT 인스턴스 또는 게이트웨이로 라우팅합니다
  - 공용 서브넷에 있기 때문이죠 따라서 사설 서브넷이 NAT을 통해 인터넷으로 접근할 수 있습니다
  - ![image](https://github.com/user-attachments/assets/1aba44a3-485a-4ef3-9370-b21e5ff0c79e)

# network acl
- ip 주소만을 가지고 제어
- nacl or network acl -> 방화벽으로 서브넷을 오고 가는트래픽을 제어
- NACL은 여기에 있으며 퍼블릭 서브넷 방어의 첫 메커니즘으로
- 인터넷에서 오고 가는 트래픽은 네트워크 ACL에 먼저 가며 EC2 인스턴스에는 아직 도달하지 않았습니다
- 보안그룹도 enl 라는 탄력적 네트워크 인터페이스또는 ec2 인스턴스를 오고 가는 트래픽을 제어
- 기본 VPC가 있으면 기본 NACL이 모든 것의 출입을 허용하기 때문입니다
- 보안 그룹은 인스턴스나 ENI에 연결 되고 네트워크 ACL은 서브넷에 연결된다는 의미입니다
- 보안 그룹은 허용 규칙만 갖지만 네트워크 ACL은 허용 규칙과 거부 규칙이 모두 가지며


# vpc flow logs
- 허용 트래픽과 거부 트래픽에 대한 모든 정보가 있기 때문입니다
- 그리고 VPC 흐름 로그 데이터는 Amazon S3와 CloudWatch Logs에 보낼 수 있고 Kinesis Data Firehose에서 볼 수 있으며

# vpc peering
- 다른 계정이나 다른 리전에 있는 상황에서 하나의 네트워크에 묶고 싶을 때
- a to b peering , ip 범위가 겹치지 않게
- 전이적이지 않는 특징 vpc 추가할수록 피어링 연결을 더 해줘야 함
- `vpc endpoint`, 프라이벳 네트워크를 이용가능하고 공개된 인터넷 네트워크 대신 역할
- aws 서비스를 프라이빗하게 액세스할 필요가 있을 때


- 온프레미스 데이터 센터와의 연결은 site to site vpn 방법이있습니다.
  - 온프레미스 vpn 어플라이언스를 aws 로 연결할 때 사용합니다.
  - ![image](https://github.com/user-attachments/assets/bb046ee6-abf8-448c-bb93-37aba54fd858)
- 다른 선택지로는 direct connect 가 있습니다.
  - 물리적으로 연결되며 프라이빗하게 연겨되며 공개 인터넷을 이용하지 않아 안전하고 빠르다.
  - ![image](https://github.com/user-attachments/assets/6bbc20c0-e164-4088-b419-b427e588f7ec)

# vpc closing comments
- NAT 게이트웨이와 NAT 인스턴스는 인터넷과 사설 서브넷을 연결합니다
- NACL 또는 네트워크 ACL은 들어오고 나가는 트래픽에 대한 무상태 서브넷 규칙 방화벽이고
- 보안 그룹의 경우 상태 유지를 하고 EC2 인스턴스 수준 또는 ENI에서 동작하며 다른 보안 그룹을 참조할 수 있습니다
- Site-to-Site VPN을 사용하여 공개 인터넷을 통해 VPN 연결을 할 수 있고
- Direct Connect를 사용하여 AWS에 비공개로 연결할 수 있습니다

# 3 tier solution architecture
- ![image](https://github.com/user-attachments/assets/5238e617-01ac-4aeb-b225-4895d8cdc3f5)
- 완벽한 사용 사례는 EFS로 네트워크 파일 시스템이자 네트워크 드라이브로 AZ마다 일래스틱 네트워크 인터페이스를 만들어 EC2 인스턴스가 이미지를 EFS에 저장하게 합니다
- ![Uploading image.png…]()


