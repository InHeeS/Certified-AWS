# x-ray (중요)
- X-Ray는 애플리케이션에 대한 시각적 분석을 제공해요
- 애플리케이션 성능 트러블슈팅이 가능하고 병목 현상을 식별할 수 있어요
- X-Ray는 호환성이 높습니다
- AWS Lambda, Elastic Beanstalk, ECS, ELB, API Gateway, EC2 인스턴스 및 어느 애플리케이션 서버와도 호환돼요, 온프레미스 서버도 마찬가지고요
- 트레이싱을 활용합니다, 트레이싱이란 엔드투엔드 방식으로 요청을 추적하는
- 이것들을 종합해서 모든 요청이나 샘플 요청을 추적할 수 있게 됩니다
- 인증에 IAM을 사용하고 미사용 데이터 암호화에는 KMS를 사용할 수 있어요
- 코드는 Java, Python, Go, Node.js, .NET으로 작성할 수 있고 AWS X-Ray SDK를 임포트해야 해요
- 코드 수정이 끝나고 두 번째로 해야 할 일은 X-Ray 데몬 설치 또는 X-Ray AWS 통합 활성화입니다
- 그걸 EC2 인스턴스에 배포하면 X-Ray 데몬이 실행되지 않아 호출을 감지하지 못하기 때문입니다
- 확실히 정리해 드릴게요, EC2 인스턴스가 있고 그 위에 애플리케이션 코드가 있어야 합니다
- ![image](https://github.com/user-attachments/assets/dba9ada2-144c-469b-a43f-c4b32d97f3a3)
- Lambda에 적절한 IAM 역할이 있는지 확인해야 해요 그리고 X-Ray 코드가 임포트됐는지 끝으로는 Lambda에서 X-Ray 활성 추적 옵션을 활성화했는지 확인해야 합니다

# x-ray instrumentation in your code
- X-Ray로 애플리케이션을 계측하려면 코드를 수정하여 X-Ray SDK를 이용해야 합니다
- ![image](https://github.com/user-attachments/assets/b6849380-811f-4bad-ad3e-6e7d80b2d6b4)
- 디버깅 하기 위한 사용자 정의 샘플링 규칙 생성 가능

# x-ray api
- 시험에서 X-Ray가 뭔가를 할 때 사용할 수 있는 API 인지 묻기 때문입니다
- ![image](https://github.com/user-attachments/assets/e9656574-0c53-4965-8ab7-4ea7bdb7d2c8)
- 요약하자면 X-Ray 데몬이 X-Ray에 쓸 때 쓰기 권한이 필요합니다 PutTraceSegments와 PutTelemetryRecords죠
- 그리고 샘플링 규칙을 가져와야 합니다 GetSamplingRules입니다 간단하죠
- ![image](https://github.com/user-attachments/assets/188b615b-bf93-4da0-8959-679f108dcff3)

# x-ray with elastic beanstalk
- Beanstalk 플랫폼에는 X-Ray 데몬이 포함돼 있어서 따로 추가할 필요가 없습니다

# ecs + x-ray integration options
- X-Ray 데몬을 실행시키는 방법으로 데몬 자체를 컨테이너로 사용합니다
- ![image](https://github.com/user-attachments/assets/e9b389a0-5c17-458d-999c-4e69187d3e43)
- ![image](https://github.com/user-attachments/assets/49db5adb-a25f-450f-b38b-f80382474de6)
  - X-Ray 데몬의 컨테이너 포트를 2000 UDP로 매핑해야 하며XRAY_DAEMON_ADDRESS라는 환경 변수를 설정하고
마지막으로 2개의 컨테이너를 네트워킹 관점에서 연결해야 한다는 것이죠
ECS와 X-Ray를 실행하는 방법을 이해하는 데 도움 되었길 바랍니다

# aws distro for opentelematry
- 오픈텔레메트리는 단일 API 셋, 라이브러리, 에이전트, 컬렉터 서비스를 사용하여 애플리케이션에서 분산된 트레이스 및 지표를 수집하는 방법입니다
- 또한 AWS 리소스나 서비스에서 메타데이터를 수집하는 데 도움이 될 수도 있어요
- 예를 들어, 트레이스를 X-Ray 서비스로 보낼 수 있고요 지표를 CloudWatch로 보낼 수 있고요, 지표나 트레이스를 Prometheus로 보낼 수도 있죠
- 리소스에 대한 컨텍스트 데이터를 수집할 수 있죠, 이 데이터는 X-Ray, Cloudwatch 및 Amazon 관리형 서비스 뿐만 아니라 오픈텔레메트리에서 지원하는 모든 파트너 모니터링 솔루션으로 보낼 수 있어요
- ![image](https://github.com/user-attachments/assets/192bfa68-2d74-4f7a-9bdb-cf0f84d83fc9)

# cloudTrail
- CloudTrail은 AWS 계정에 대한 거버넌스(Governance), 규정 준수 및 감사를 수행합니다
- 이는 콘솔, SDK, CLI, 다른 AWS 서비스에서 만들어진 AWS 계정 내 모든 이벤트의 기록과 API 호출을 얻게 해줍니다
- ![image](https://github.com/user-attachments/assets/8e7faa56-afae-4b9a-9b0e-5e82b2d75293)
- 리소스나 AWS 계정을 수정하는 것은 모두 CloudTrail에 나타날 것입니다
- CloudTrail에는 3종류의 이벤트가 있습니다 첫 번째는 관리 이벤트라고 합니다
  - 이것은 AWS 계정에 있는 리소스에서 수행되는 작업을 의미합니다
- data event 두번째
  - 대용량 작업이기 때문이죠 데이터 이벤트가 무엇일까요? GetObject, DeleteObject PutObject와 같은 Amazon S3 객체 수준의 활동에서 보이듯이 S3 버킷에서 많이 발생하는 것들이죠
- CloudTrail의 세 번째 이벤트는 인사이트(Insight) 이벤트입니다
  - 예를 들어 부정확한 리소스 프로비저닝, 서비스 제한 초과, AWS IAM 작업 폭주, 주기적인 유지보수 활동의 격차 등이 있죠
  - 무언가 변경될 때 또는 변경을 시도할 때마다 비정상적인 패턴을 감지합니다
  - 이런 이상치, 인사이트 이벤트는 CloudTrail 콘솔에 나타납니다 원한다면 Amazon S3에 보낼 수 있습니다
- cloud trail evnets retention 은 90일 동안 보관되며 삭제된다. 그 이상은 s3 로그에 보내야합니다.  Athena를 이용하여 분석할 수 있습니다

# api 호출을 가로 채기 위한 eventbridge
- ![image](https://github.com/user-attachments/assets/1b9b79a7-67a0-4046-90fe-1d492c973918)
- ![image](https://github.com/user-attachments/assets/44aa0f0d-1977-4d30-a17b-6e272ae16133)

# cloudtrail vs cloud watch vs x-ray
- CloudTrail은 사용자, 서비스 또는 AWS 콘솔에서 계정에서 이루어진 API 호출을 감사하는 겁니다 승인되지 않은 호출을 감지하거나 API 호출로 인한 변경의 근본적인 원인을 찾을 때 유용합니다, 지표를 사용하고 알림을 보냅니다.
- 즉, CloudTrail은 API 호출 CloudWatch는 모니터링이 중점적입니다
- X-Ray는 자동화된 추적 분석과 중앙 서비스 맵 시각화를 해주기 때문에 분산 서비스에서 큰 숲을 보기에 좋습니다
- 오류 및 오류 분석 같은 항목을 보는 데 정말 유용합니다 말씀드렸듯 분산 시스템 전체에서 추적 요청을 얻을 수도 있습니다

- CloudWatch는 전반적인 지표를 위해 존재하고, X-Ray는 훨씬 더 세분화되고 추적 지향적인 서비스이며 CloudTrail은 API 호출을 감사하기 위한 겁니다
