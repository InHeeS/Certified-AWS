# Monitoring
- cloudwatch 지표를 수집 가능합니다.
- 로그를 수집하고 로그 파일을 분석합니다.
- 실시간으로 반응해서 알람을 보냅니다.
- 마이크로서비스 분산 추적이 가능합니다.
- cloudtrail 로는 api 호출에 대한 내부 모니터링 수행 및 리소스 변경 내용 감사 가능

# Cloudwatch metric
- CPUUtilization, NetworkIn 그리고 지표의 동작을 통해서도
- 지표는 타임스탬프를 가지며 대시보드 생성 가능
- ELB나 오토 스케일링, EBS EC2, EFS 등과 같은 서비스를 기반으로 하고 있죠
- csv, 숫자, 도표 등등 여러 대시보드 가능

# cloudwatch custom metrics
- CloudWatch나 디스크 공간 애플리케이션에 로그인한 사용자 수 등이 있죠 이를 위해서 PutMetricData라는 API 호출을 사용합니다
- 값의 이름, 단위, 값 등 배열과 스토리지 해상도 등을 지정할 수 있습니다
- 1분 또는 60초에 한 번씩 푸시하는 표준 사용자 지정 지표나 고해상도 지표가 있죠 이 경우에는 1, 5, 10, 30초마다 지표를 푸시합니다 사용자 지정 지표는 푸시 하는 방향이 과거든 미래든 상관없습니다

# cloudwatch logs
- 애플리케이션 로그를 aws에 저장하기에 가장 좋은 장소
- s3로 배치 보내며, kinesis data stream 으로 스트림 가능하며 firehose, lambda, opensearch 등이 있음
- kms 기반 암호화를 이용해 자신의 키로 암호화 가능
- vpc flow, api gateway, cloudtrail (필터 기반), route 53(dns query)
- ![image](https://github.com/user-attachments/assets/89cafa15-421f-40f8-8abb-aeeb1eb0536b)
- ![image](https://github.com/user-attachments/assets/2a513df6-7707-46d3-97e6-d282bd3c52e3)


# cloudwatch logs for ec2 for cloudwatch agent
- 따라서 EC2 인스턴스에서 CloudWatch Log Agent를 실행하여 CloudWatch Logs로 로그를 전송해 줍니다
- 반면 Unified Agent는 추가 시스템 수준의 지표를 수집하죠 RAM, 프로세스 등인데 다음 슬라이드에서 보여드리겠습니다
- 결론은 CloudWatch Unified Agent를 사용하면 EC2 인스턴스에 대하여 일반적인 모니터링보다 더 자세한 지표를 알 수 있다는 겁니다

# cloudwatch logs metric filter
- 필터 표현식으로 로그를 볼 수 있다는 겁니다
- ![image](https://github.com/user-attachments/assets/35f85a04-e671-4a94-bbc9-9d2e531dfaeb)

# cloudwatch alarms
- 알람의 주요 대상으로 첫 번째는 EC2 instances에서 정지, 종료, 재부팅 그리고 복구 작업을 할 수 있습니다
- 두 번째는 Auto Scaling 입니다 예를 들면 scale out, scale in을 트리거합니다
- 마지막, SNS 서비스에 알림을 보냅니다
- CloudWatch alarms는 단일 지표만 모니터링 합니다 만약 여러 지표를 보려면 Composite Alarms를 사용해야 합니다
- ec2 instance recovery
  - ![image](https://github.com/user-attachments/assets/0306080a-2436-4a00-b914-ff13d06b344b)
 
# cloudwatch synthetics canary
- 여러분이 스크립트를 정의하면 그 스크립트가 프로그램상에서 고객의 작업을 똑같이 재현합니다
- API Canary는 REST API의 기본 읽기 쓰기 함수를 테스트하고요

# amazon evnetbridge
- 클라우드에 작업을 스케줄링할 수 있습니다, 스크립트를 예약할 수 있죠
- ![image](https://github.com/user-attachments/assets/695cf820-50b3-45ce-bc9b-51cdd5a6c75e)
- ![image](https://github.com/user-attachments/assets/27e1cb0d-9bff-45af-87bf-1106ddee5991)
- 파트너 이벤트 버스라는 것도 있는데요 AWS가 파트너와 결합된 겁니다 SaaS 파트너인 경우가 많겠죠
- 기본 이벤트 버스가 있으므로 계정 내에서 일어나는 이벤트에는 모두 반응할 수 있습니다
- 파트너 이벤트와 사용자 이벤트 버스로 가능한 사용자 이벤트가 있다는 것도 기억하세요
- 스키마 레지스트리 기능이 있었고 리소스 기반 정책이 있습니다 계정 간 이벤트 버스 기능을 공유할 수 있죠
- AWS에 여러 계정이 있는데, 중앙 계정의 이벤트 버스에서 몇몇 이벤트를 중앙 집중식으로 관리할 거라고 해보죠,
- 리소스 정책을 생성해 다른 계정의 이벤트를 수용하도록 해야 합니다
