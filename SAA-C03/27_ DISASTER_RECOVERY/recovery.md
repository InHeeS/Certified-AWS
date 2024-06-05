# 재해 복구

온프레미스를 기본 데이터 센터로 두고
재해 발생 시 클라우드를 사용하는 방식을
하이브리드 복구라고 합니다

*복구 시점 목표를 의미하는 RPO
복구 시간 목표를 의미하는 RTO*

먼저 RPO 즉 복구 시점 목표란
얼마나 자주 백업을 실행할지
시간상 어느 정도 과거로 되돌릴 수 있는지를 결정합니다

백업 및 복구는 RTO가 작은 반면
파일럿 라이트와 웜 대기 다중 사이트는
비용이 더 들지만 RTO가 빠릅니다

정리하면 이렇습니다 *백업 및 복구*는 아주 쉽고
비용이 저렴한데 RPO와 RTO가 높아요

두 번째는 파일럿 라이트입니다
애플리케이션 축소 버전이
클라우드에서 항상 실행되고
*보통 크리티컬 코어가 되는데*
이를 파일럿 라이트라고 합니다
이때 RPO와 RTO가 낮아집니다

웜 대기는 시스템 전체를 실행하되
최소한의 규모로 가동해서 대기하는 방법입니다
이건 비용이 조금 더 드는데요
ELB와 EC2 오토 스케일링이
동시에 실행되기 때문입니다 어쨌든 RPO와 RTO는 줄어듭니다

마지막으로 다중 사이트 혹은 핫 사이트 접근입니다
몇 분, 몇 초 정도로 RTO가 낮지만 비용이 굉장한데
AWS와 온프레미스에서 두 완전 프로덕션 스케일을 얻게 돼요
Route 53가 기업 데이터 센터와 AWS Cloud에
요청을 라우팅할 수 있는데
이를 액티브-액티브 유형 설정이라고 해요

*백업에는 EBS 스냅샷과\
RDS로 자동화된 스냅샷과 백업 등을 사용해요
S3, S3 IA, Glacier 등에 스냅샷을 규칙적으로 푸시할 수 있죠
수명 주기 정책 실행도 가능합니다
백업이 다른 리전에 있길 바란다면
리전 간 복제도 가능하고요
온프레미스에서 클라우드로 데이터 공유 시
Snowball과 Storage Gateway가 유용합니다
고가용성을 위해서는 Route 53를 사용해
DNS를 다른 리전으로 옮기면 돼요 아주 쉽고 유용한 방법이랍니다*

인프라가 무사하도록 카오스 몽키를 실행해
이것저것 무작위로 종료하는 겁니다
인프라 기반을 단단히 다져
어떤 장애에도 끄떡없도록 말이죠

# DMS Database Migration Service

rdbms 시스템을 온프레미스 시스템에서 aws cloud 로 마이크그레이션하할대 dms 를 사용합니다. 복원력이 있고 자가 치유가 가능한다는 점이 장점이죠

그럼 소스 데이터베이스와 타깃 데이터베이스의 엔진이 다른 경우에는 어떻게 될까요?

그럴 때는 소위 AWS SCT를 사용해야 해요, SCT는 스키마 변환 도구를 의미하죠,

하지만 시험을 위해 기억해야 할 점은 동일한 데이터베이스 엔진 간에 마이그레이션을 할 때는SCT를
사용할 필요가 없다는 점이예요

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/95d9503a-8b1a-42a1-9ee4-a599dba8e79f)

# Aurora MySQL로 마이그레이션

RDS 데이터베이스를 Aurora MySQL로 옮기고자 할 경우
첫 번째 방법은 RDS MySQL 데이터베이스의 스냅샷을 생성해서
이 스냅샷을 MySQL Aurora 데이터베이스에서 복원하는 것입니다

두 번째는 좀 더 지속적인 방법으로
Amazon Aurora 읽기 전용 복제본을
RDS MySQL에 생성하는 것입니다.

MySQL 데이터베이스가 RDS 외부에 있는 경우에는
Percona XtraBackup 기능을 사용하여 백업할 수 있습니다

또 다른 방법은 mysqldump 기능을
MySQL 데이터베이스에서 실행하여
기존 Amazon Aurora 데이터베이스로 출력값을 내보내는 겁니다

마지막 선택지는 Amazon DMS를 이용해서 두 데이터베이스가 가동되는 채로
이 데이터베이스 간 지속적인 복제를 진행하는 방법이 있습니다

해당 백업을 Amazon S3에 두고 데이터를 가져오기 위해
aws_s3 Aurora 확장자를 사용해서
새로운 데이터베이스를 생성하는 방법도 있습니다

# On-Premise strategy with AWS

정리하자면 온프레미스 VM이 많은 경우 이를 클라우드에 백업하고 싶을 때
가져오기, 내보내기 기능을 통해 VM을 EC2에서 온프레미스 환경으로
다시 빼올 수도 있습니다

AWS 애플리케이션 Discovery Service는
온프레미스의 정보를 모아주고 마이그레이션을
계획할 수 있게 해 주는 서비스인데요

온프레미스에서 클라우드로
대량의 마이그레이션 할 때 유용합니다

마지막으로 SMS, 즉 AWS Server Migration Service가 있습니다
온프레미스의 라이브 서버들을 AWS로 증분 복제할 때 쓰는데요

*온프레미스에서 가능한 것은
온프레미스와 EC2에 대한 VM 가져오기와 내보내기가 있었고
애플리케이션 Discovery Service AWS와 같은 마이그레이션 서비스
Migration Hub DMS, SMS 등의
서비스도 있었습니다*

# AWS Backup 

완전 관리형 서비스이며 aws 서비스 간의 백업을 중점적으로 관리하고 자동화 합니다. 

실제 중앙 시스템이 ㅇ벗고 사용자 지정 스크립트나 몌뉴얼을 만들 필요도 없습니다. 

또한 리전 간 백업을 지원하므로
한 곳의 재해 복구 전략을
다른 리전에 푸시할 수 있습니다
그리고 계정 간 백업도 지원해요

AWS Backup에서 알아둘 또 다른 기능은 볼트(Vault) 잠금입니다
WORM(Write Once Read Many) 정책을 시행하면
백업 볼트(Vault)에 저장한
백업을 삭제할 수 없게 됩니다
이 기능이 활성화되면 루트 사용자 자신도 백업을 삭제할 수 없어요

# 클라우드 이동

클라우드로 마이그레이션하려면, 마이그레이션을 계획해야 합니다

한 가지 방법은 AWS Application Discovery 서비스로 마이그레이션을 계획하는 겁니다
서버를 스캔하고 마이그레이션에 중요한 서버 설치 데이터 및
종속성 매핑에 대한 정보를 수집합니다

하나는 Connector를 사용하는 Agentless Discovery입니다
가상 머신, 구성, CPU와 메모리 및 디스크 사용량과 같은
성능 기록에 대한 정보를 제공합니다

온프레미스에서 AWS로 이동하는 가장 간단한 방법은, AWS Application Migration Service
즉 MGN이라는 걸 사용하는 겁니다

데이터 센터에 설치된 복제 에이전트가 디스크를 연속적으로 복제할 겁니다
예를 들어, 저비용 EC2 인스턴스, EBS 볼륨이
이런 데이터 복제를 갖게 됩니다

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/882d78b0-d043-4250-bccb-7eb9fb16eb2e)

# transferring large amunt of data into aws 

먼저 공용 인터넷을

사용하는 방법이 있고 역시 또 공용 인터넷을 사용해
사이트 간 VPN을 설치하는 방법도 있습니다
이 방법의 장점은 설치가 빠르고
바로 연결이 가능하다는 거죠

다음 반안으로 Direct Connect를 사용한다면 10배정도 빨라집니다.

Snowball은
AWS로 첫 번째 데이터를 보내는 속도를 올리기 아주 유용하죠

시험에서는 가장 쉽고 빠르고 안정적으로 AWS에
데이터를 보내는 방법을 묻는데요
데이터셋의 크기에 따라 답이 달라집니다

# vmware cloud on AWS 

상황을 소개하자면 온프레미스에 데이터 센터가 있을 때
VMware Cloud로 데이터 센터를 관리하는 경우가 있습니다

vSphere 기반 환경과 가상 머신을
VMWare Cloud를 통해 관리하는 거죠

이 서비스를 이용하면 전체 VMware Cloud의 인프라를 AWS에서 확장함으로써
vSphere, vSAN, NSX 등에서 사용할 수 있죠

그리고 AWS 클라우드를 사용하므로
다양한 AWS 서비스를 이용할 수 있게 됩니다

# quiz

Question 6:
AWS DataSync가 지원하지 않는 로케이션을 고르세요.

Answer : Amazon EBS

Question 10:
회사는 MySQL용 RDS를 메인 데이터베이스로 사용하고 있는데 최근 데이터베이스 관리 문제, 성능 문제, 확장성 문제가 나타나고 있습니다. 의논한 결과, 더 높은 성능과 낮은 복잡도, 관리 작업 감소라는 이점을 얻기 위해 MySQL용 Aurora를 사용하기로 결정했습니다. MySQL용 RDS에서 MySQL용 Aurora로 마이그레이션하는 데 가장 적합하고 경제적 방식은 무엇입니까?

Answer : MYSQL 용 RDS 에서 스냅샷을 생성하고 MYSQL 용 AURORA에서 복원한다. 

# AWS 이벤트 처리와 다양한 처리방법 그리고 제약점

첫 번째는 SQS와 Lambda를 사용하는 방법입니다

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/c87bd5d9-a9d9-4f21-828f-033bdf361bce)

팬아웃 패턴은 다음과 같습니다. 
따라서 이 방법은 안정성도 낮고 구조도 깔끔하지 않습니다

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/90ac4c0a-92dc-472a-8bde-a720cf7dc274)

Amazon S3 버킷이 특정 이벤트에만 반응하도록 설정할 수 있어
객체 생성, 삭제 복원, 복제본 생성 시 알림을 보내도록 할 수 있고
객체 이름별로 필터링도 가능하죠

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/187eb545-5186-4e3c-aa43-6a9e4856f571)

끝으로 API Gateway 등을 이용하는 AWS의 외부 이벤트가 있습니다

# 캐싱 전략


![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/8fb1e59f-15a9-4c33-951f-b03fc85b058e)


마지막으로 Amazon S3의 데이터베이스에는 캐싱 기능이
없다는 걸 알아두세요

# 네트워크 토론과 보안

NLB 는 보안 그룹이 없고 모든 트래픽이 지나가기 때문에 EC2 인스턴스는 엣지에서 클라이언트의 공용 IP를 감지합니다. 

ALB를 살펴보면ㅇWAF를 설치하거나 웹 애플리케이션 방화벽을 설치해 IP를 차단 가능합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/acb8def2-becc-4fd3-8460-4ab791461524)

# 고성능 컴퓨팅 혹인 HPC 

HPC의 작업을 돕는 AWS 서비스는 무엇이 있을까요?
첫 번째는 데이터 관리 방식과
AWS로 데이터를 전송하는 방법에 관한 겁니다

분배된 형태로 동작할 경우
클러스터 유형의 EC2 배치 그룹을 사용하면
최고의 네트워크 성능을 발휘하죠

그러면 EC2 인스턴스의 성능을 더 향상하는 방법은 무엇일까요?
최근에 가장 흔히 쓰이는 방법은
Elastic Network Adapter(ENA)입니다
네트워크 속도를 100Gbps까지 올려주죠

ENA와 Intel 모두 여러분의 인스턴스에서
EC2 Enhanced Networking을 이용할 수 있게 합니다
더 나아가서 Elastic Fabric Adapter(EFA)를 사용해도 됩니다

HPC, 고성능 컴퓨팅을 위해 개선된 ENA인데요
Linux에서만 사용 가능합니다
노드 간 소통이나 밀집된 워크 로드 처리에 좋아요
분산 계산과 같은 경우입니다

분산 계산과 같은 경우입니다
그 이유는 ENA가 사용하는 게 Message Passing Interface(MPI) 표준이거든요

EFA를 사용하면 OS를 우회하여 보다 높은 네트워크 성능을 제공할 수 있죠

*AWS ParallelCluster는 오픈 소스 클러스터 관리 도구로
HPC를 AWS에 배포합니다
텍스트 파일로 구성해서 AWS로 배포하는 겁니다
그리고 VPC와 서브넷 및 클러스터 타입과
인스턴스 타입 생성을 자동화합니다*

단일 서비스가 아니라 여러 옵션과 서비스의 결합이에요

# 솔루션 아키텍처

아시다시피 EC2 인스턴스는
기본적으로 하나의 가용 영역에서 실행됩니다


![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/01913876-452c-4f16-9529-d4e93af4b164)


이게 바로 EBS 볼륨으로 고가용성 EC2 인스턴스를 만드는 법입니다

# QUIZ 

Question 1:
S3 버킷으로 업로드되는 객체를 처리하는 서버리스 애플리케이션으로 작업하고 있습니다. S3 버킷에 S3 이벤트를 구성해 객체가 업로드될 때마다 Lambda 함수가 호출되도록 만들었습니다. 프로세싱되지 않는 이벤트는 추후 처리를 위해 데드 레터 대기열(DLQ)로 전송시키려 합니다. 이런 경우, 다음 중 어떤 AWS 서비스를 선택해야 할까요?

Answer : S3 Events (비동기적이다. )

Question 3:
고성능 컴퓨팅(HPC)을 수행하기 위해 EC2 인스턴스를 클러스터 배치 그룹으로 배포했습니다. EC2 인스턴스들 간의 네트워크 성능을 최대화하려 합니다. 무엇을 사용해야 할까요?

Answer : Elastic Fabric Adapter
