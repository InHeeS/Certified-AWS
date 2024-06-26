# ELB 개요

확장성은 애플리케이션 시스템이 조직을 통해 더 많은 양을 처리할 수 있다는 의미입니다. 
수직 확장성은 데이터베이스와 같이 분산되지 않는 시스템에서 흔시 사용됩니다. rds, elasticCache
수평 확장성은 대체로 분배 시스템을 의미합니다. 

고가용성은 애플리케이션 또는 시스템을 적어도 둘 이상의 aws의 az나 데이터 센터에서 가동 중이라는 것을 의미합니다. 
목표는 데이터 센터에서의 손실에서 살아남는 것입니다. 센터 하나가 멈춰도 작동 가능하게 하는 것 입니다.
동일 애플리케이션의 동일 인스턴스를 다수의 az에 걸쳐 실행하는 경우를 의미합니다. 

# ELB

로드 밸랜서는 서버 혹은 서버셋으로 트래픽을 백엔드나 다운스트림 ec2 인스턴스 또는 서버들로 전달하는 역할을 합니다.
상태 확인은 엘라스특 로드 밸런서가 ec2 인스턴스의 작동이 올바르게 되고 있는지의 여부를 확인하기 위함입니다. 

ec2 인스턴스의 보안 그룹을 로드 밸런서의 보안 그룹으로 연결하여 강화된 보안 메커니즘을 완성합니다. 

# ALB

http 전용 로드 밸런서로 머신 간 다수 http 애플리케이션의 라우팅에 사용됩니다. 머신들은 대상 그룹으로 묶이게 됩니다. 
마이크로 서비스나 컨테이너 기반 애플리케이션에 가장 좋습니다. docker난 ecs의 경우에도 가장 적합합니다. 

alb 는 여러 대상 그룹으로 라우팅할 수 있으며 상태확인은 대상 그룹 레벨에서 이뤄집니다. 또한 서버의 사설 ip가 대상 그룹에게 지정되어야 합니다. 
alb 는 리다이렉션 라우팅 규칙을 생성 가능합니다. ec2 인스턴스가 클라이언트의 ip 를 알기 위해서는 http 요청에 있는 추가 헤더인 x-forwarded-port와 proto를 확인해야 합니다. 

# 네트워크 로드 밸랜서

L4 로드 밸런서이므로 tcp 와 udp 트래픽을 다룰 수 있습니다. 네트워크 로드 밸랜서의 성능은 매우 높습니다. 초당 수백만 건의 요청을 처리하며 alb에 비해 지연 시간도 짧습니다. 
nlb 는 100ms 이며 가용 영역별로 하나의 고정 ip를 갖습니다. 탄력전 ip주소를 각 az에 할당 가능합니다. 여러개의 고정 ip를 가진 애플리케이션을 노출할 때 유용합니다. 
nlb 는 tcp or udp 트래픽을 ec2 인스턴스로 리다이렉트할수 있습니다. ip주소를 등록하는데 반드시 하드코딩 되어야 하며 프라이빗 ip여야 합니다. 
nlb 를 alb 앞에 배치하는 경우는 nlb 대문에 고정 ip 주소를 얻으며 alb 덕분에 http 유형의 트래픽을 처리하는 규칙을 얻을 수 있습니다. 
nlb 의 상태 확인은 세가지 프로토콜을 지원합니다. tcp, http, https 

# GWLB 

gwlb 는 배포 및 확장과 aws의 타사 네트워크 가상 어플라이언스의 플릿 관리에 사용됩니다. 
모든 트래픽이 방화벽을 통과하거나 침입 탐지 및 방지 시스템에 사용합니다. idps 나 심층 패킷 분석 시스템 도는 일부 페이로드를 수정 가능하지만 네트워크 수준에서 가능합니다.
gwlb 를 생성하면 이면에서는 vpc에서 라우팅 테이블이 업데이트 됩니다. 라우팅 테이블이 수정되면 모든 사용자 트래픽은 gwlb를 통과합니다. 그리고 gwlb는 가상 어플라이언스 대상구릅 전반으로 트래픽을 확산합니다. 
모든 트래픽은 어플라이언스에 도달하고 어플라이언스는 트래픽을 분석하고 처리합니다. 

모든 로드밸런서보다 낮은 수준에서 실행됩니다. 투명 네트워크 게이트웨이로 vcp의 모든 트래픽이 gwlb가 되는 단일 엔트리와 출구를 통과합니다. 
6081 번 포트의 geneve 프로토콜을 사용하면 바로 gwlb가 됩니다. 

# Sticky Sessions or Session Affinity

개념은 로드 밸런서에 2가지 요청을 수행하는 클라이언트가 요청에 응답하기 위해 백엔드에 동일한 인스턴스를 갖는 것 입니다.  
모든 ec2 인스턴스 전반으로 모든 요청을 확산하는 것과는 다른 동작으로 쿠키가 있습니다. 클라이언트에서 로드 밸런서로 요청의 일부로서 전송되는 것으로 고정성과 만료 기간이 있습니다. 
즉 쿠키가 만료되면 클라이언트가 다른 ec2 인스턴스로 리다이렉션 된다는 것입니다. 고정성을 활성화하면 백엔드 ec2 인스턴스 부하에 불균형을 초래할 수 있습니다. 

고정 세션에는 2가지 유형의 쿠키가 있습니다.
애플리케이션 기반 쿠키는 대상으로 생성된 사용자 정의 쿠키로 애플리케이션에서 생성됩니다. 
기간 기반 쿠키는 특정 기간을 기반으로 만료되며 그 기간이 로드 밸랜서 자체에서 생성되는 겁니다. 
두가지 유형 모두 특정 이름이 있다는 것정도 기억하면 됩니다. 

# 교차 영역 밸런싱

각각의 로드 밸런서 인스턴스가 모든 가용 영역에 등록된 모든 인스턴스에 부하를 고르게 분배합니다. 
다른 방법으로는 영역을 교차하지 않고 부하를 분산하는 방법이다. 그렇게 되면 탄력적 로드 밸런서 노드의 인스턴스로 분산됩니다. 즉, 각 가용 영역 안에서 부하가 분산됩니다. 
alb 는 데이터를 다른 가용 역영으로 옮기는 데 비용이 들지 않습니다. 왜냐하면 교차 영역 로드 밸런싱이 기본으로 활성화 되어 있기 때문입니다.  

# SSL/TLS

ssl 인증서는 클라이언트와 로드 밸랜서 사이에서 트래픽이 이동하는 동안 암호화 해줍니다. 보안 소켓 계층을 의미하고 연결을 암호화하는 데 사용됩니다. 
tls 는 최근에 사용되며 여전히 ssl이라고 불립니다 SSL 인증서는 만료 날짜가 있어 주기적으로 갱신해 인증 상태를 유지해야 합니다. 
백엔드에서는 http 로 ec2 인스턴스와 암호화 되지 않는 상태로 vpc 를 이용해서 트래픽을 프라이빗 네트워크를 통해 안전하게 보호 합니다. 
http 리스너를 구성할 대 반드시 https 리스너로 해야 합니다. https 는 여러분이 원하는 대로 보안 정책을 지정할 수 있다는 겁니다. 

sni 는 여러개의 ssl 인증서를 하나의 웹 서버에 로드해 하나의 웹 서버가 여러 개의 웹 사이트 지원을 가능하게 해줍니다.
클라이언트가 대상 서버의 호스트 이름을 지정하도록 합니다. 때문에 ssl 인증서가 여러 개 있다면 alb나 nlb 둘중 하나 입니다. 
sni 는 서버 이름 지정을 써서 여러 개의 대상 그룹과 웹사이트를 지원할 수 있습니다. 다중 ssl 인증서를 사용하기 위함입니다. 

# *연결 트레이닝(중요)*

alb, nlb는 등록 취소 지연이라고 부릅니다. 인스턴스가 등록 취소, 혹인 비정상인 성태에 있을 때 인스턴스에 어느 정도의 시간을 주어 인-플라이트 요청, 즉 활성 요청을 완료할 수 있도록 하는 기능입니다.
연결 드레이닝 파라미터는 매개변수로 표시 가능한데 0으로 설정하면 전부 다 비활성화한다는 의미입니다. 즉 드레이닝도 일어나지 않는다는 뜻입니다. 짧은 요청의 경우 낮은 값으로 설정하면 좋습니다. 

# ASG

ec2 인스턴스 생성 api호출을 통해 서버를 빠르게 생성하고 종료하기 위함입니다. 로드 밸런서와 페어링하는 경우 asg에 속한 모든 ec2인스턴스가 로드 밸런서에 연결됩니다. 
elb 상태 확인을 통해 ec2 인스턴스의 상태를 확인하고 로드 밸런서가 비정상이라 판단하는 ec2 인스턴스를 asg가 종료할 수 있습니다. 
인스턴스 속성을 기반으로 asg 를 생성하려면 시작 템플릿을 생성해야 합니다. 
cloudwatch 경보를 기반으로 asg를 스케일 인 및 아웃이 가능합니다. 

오토 스케일링 그룹의 스케일링 정책에 대해 살펴봅니다. 
동적 스케일링 정책은 세가지 유형이 있습니다.
1. 추적 스케일링으로 단순합니다. 기준선을 세우고 상시 가용이 가능합니다.
2. 단순과 단계 스케일링은 cloudwatch 경보를 설정하고 용량을 두 유닛 추가하도록 설정합니다.
3. 예약 된 작업은 사용 패턴을 바탕으로 스케일링을 예상합니다.

스케일링 기반이 될 지표가 있습니다.
1. cpu 사용률
2. ec2 인스턴스에 대해 해당 네트워크에서 병목 현상이 발생할 것으로 판단 된다면 평균 네트워크 입출력량을 기반으로 스케일링을 수행해서 특정 임계값에 도달할때 스케일링을 수행하도록 설정 가능합니다.
3. 스케일링 휴지는 인스턴스의 추가 또는 삭제를 막론하고 기본적으로 300 초의 휴지 기간을 갖는 것입니다. 이를 통해 새로운 인스턴스가 안정화 될수 있습니다. 활성화 시간이 빨라지면 휴지 기간 도한 단축되기 때문에 좋습니다. 
