# IP

공용 IP는 전체 웹에서 유일한 것이여야 하는데 즉 두개 이상의 기기가 같은 공용 IP를 가질 수 없다는 것입니다.

사설 IP는 오질 사설 네트워크 안에서만 식별될 수 있으며 IP가 사설 네트워크 안에서면 유일한 것이면 됩니다. 두개의 다른 사설 네트워크 즉 두개의 다른 회사는 같은 사설 IP를 가질 수 있고 전혀 문제가 되지 않습니다. 

탄력적 IP의 경우 EC2 인스턴스를 시작하고 중지할 때 공용 IP를 바꿀 수 있습니다. 한번에 한 인스턴스에만 첨부할 수 있습니다. IP주소가 탄력적이면 한 인스턴스에서 다른 인스턴스로 빠르게 이동함으로써 인스턴스 도는 소프트웨어의 오류를 마스킹할 대 사용할 수 있습니다. 결론적으로 탄력IP는 사용하지 않는것 이좋습니다. 대신 임의의 공용 IP를 써서 DNS 이름을 할당하는 것이 좋습니다. 도는 로드밸런서를 사용해서 공용IP를 전혀 사용하지 않을 수도 있습니다. 

EC2 기기에 SSH할 댄 사설 IP를 사용할 수없는데요 왜냐하면 VPN을 쓰지 않는 이상 같은 네트워크에 있는 것이 아니기 때문입니다. 때문에 VPN이 없다면 공용 IP만 사용할 수 있습니다. 기기를 멈웠다가 다시 시작하면 공용 IP가 바뀔 수도 있습니다. 

AWS 는 사설 네트워크에 연결되어 있지 않기 때문에 SSH 로 접속이 안됩니다. 

- 탄력적 IP 할당

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/ce6a9284-ff5f-4a0b-b721-2e4b64a551af)

# 배치 그룹

AWS의 하드웨어와 직접적인 상호 작용을 하지는 않지만 EC2 인스턴스가 각각 어떻게 배치되기를 원하는지 AWS에 알려주는 겁니다. 

- 클러스터 배치그룹은 단일 가용 영역내에서 지연 시간이 짧은 하드웨어 설정으로 인스턴스를 그룹화 합니다. 모든 EC2 인스턴스가 동일한랙에 있다는 겁니다. 즉, 동일한 하드웨어와 동일한 가용 영역에 있다는 겁니다.  지연 시간이 매우 짧은 속도 정도의 네트워크를 원하기 때문에 동일한 랙에 배치하며 놀라운 네트워크를 갖게 됩니다. 단 단점은 랙에 실패가 발생하면 즉 하드웨어에 실패가 발생하면

모든 EC2인스턴스가 동시에 실패한다는 겁니다. 빅데이터 작업에 수행할 수 있습니다. 극히 짧은 지연 시간과 높은 네트워크 처리량을 필요로 하는 경우 실패를 감수할 수 있을때 유용 합니다. 

- 분산 배치 그룹은 인스턴스가 다른 하드웨어에 분산된다는 의미입니다.또한 크리티컬 애플리케이션이 있는 경우 사용합니다.

  분산 배치 그룹은 실패 위험을 최소화 하려고 합니다. 모든 EC2 인스턴스가 다른 하드웨어에 위치하게 됩니다. 장점은 여러 가용 영역에 걸쳐 있을 수 있으며 동시 실패 위험이 감소합니다. 단 가용 영역당 7개의 인스턴스로 제한이 된다는 겁니다. 가용성을 극대화하고 위험을 줄여야 하는 애플리케이션입니다. 즉 크리티컬 애플리케이션의경우 입니다. 

- 분할 배치그룹은 인스턴스를 분산하려는 것입니다. 다만 이건 여러 파티션에 인스턴스가 분할되어 있고 가용 영역내의 다양한 하드웨어 랙 세트에 의존합니다. 즉 인스턴스가 여전히 분산되어 있지만 다른 실패로부터 격리되지 않았다는 겁니다.

  여러 가용 영역의 파티션에 인스턴스를 분산하며 가용 영역당 7개의 파티션이 올 수 있습니다. 파티션들은 동일한 리전의 여러 가용 영역에 걸쳐 있을 수 있습니다. 파티션들 전반에 걸쳐 데이터와 서버를 퍼뜨려 두도록 파티션 인식 가능한 애플리케이션의 경우에 사용합니다. HDFS HbaseCassandra 및 Apache Kafka를 사용하여 파티션을 인식하는 빅 데이터 애플리케이션이 되겠죠

# Elastic Network Interfaces (ENI)

탄력적 네트워크 인터페이스를 가리키며 VPC의 논리적 구성 요소이며 가상 네트워크 타드를 나타냅니다. ENI는 EC2인스턴스가 네트워크에 액세스할수 있게 해주죠 ENI는 하나 이상의 보안 그룹을 연결할 수있습니다. EC2 인스턴스와 독립적으로 ENI를 생성하고 즉시 연결하거나 장애 조치를 위해 EC2인스턴스에서 이동시킬 수 있습니다. ENI는 특정 가용 영역 즉 AZ에 바인딩 됩니다. 

- 네트워크 인터페이스 생성

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/a72bfade-6a1d-4b18-a016-60167c9d0be9)


# EC2 절전 모드 

인스턴스를 중지하면 EBS 데스크 데이터는 다시 시작할 때까지 그대로 유지됩니다. 인스턴스가 절전 모드가 되면 ram에 있던 인 메모리 상태는 그대로 보존됩니다. 인스턴스 부팅이 빨라지는 거죠 절전 모드가 되고 백그라운드에서 ram 에 기록되었던 인 메모리 상태는 루트 경로의 EBS 볼륨에 기록되기 때문에 루트 EBS 볼륨을 암호화 해야 하고 볼륨 용량도 RAM을 저장하기에 충분해야 합니다. 

사용 사례로는 오래 실행되는 프로세스를 갖고 있고 중지하지 않을 때 RAM 상태를 저장하고 싶을때, 빠르게 재부팅을 하고 싶을 때, 서비스 초기화가 시간을 많이 잡아먹어 서비스가 중단 없이 인스턴스를 절전 모드로 전환하고 싶을때 입니다. 

- 인스턴스 절전 모드 설정

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/a83e6fa7-9625-41a3-a636-9487cf0dc359)

- 루트 EBS 볼륨 암호화 확인

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/a75baf4f-519e-4ae9-9d2f-fbe174fcfb33)

# quiz

탄력적 네트워크 인터페이스(ENI)는 다른 AZ에 있는 EC2 인스턴스와 연결될 수 있습니다. ( x )

-> 탄력적 네트워크 인터페이스(ENIs)는 특정 AZ로 국한됩니다. 다른 AZ에 있는 EC2 인스턴스에 ENI를 연결할 수는 없습니다.
