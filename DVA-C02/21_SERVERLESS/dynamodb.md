# dynamo db
- RDS에는 수평적 쓰기 스케일링이 없기 때문이죠 따라서 NoSQL 데이터베이스를 소개합니다
- 이 데이터베이스는 조인과 같은 쿼리를 지원하지 않거나 제한적으로 지원합니다
- DynamoDB는 완벽하게 관리되는 고가용성의 NoSQL 데이터베이스이며 여러 AZ에 걸쳐 즉시 복제할 수 있습니다
- 대규모 워크로드로 확장되고 완벽하게 분산됩니다
- 이번 섹션에서 배울 DynamoDB Streams로 이벤트 기반 프로그래밍을 활성화할 수 있습니다
- 다른 스토리지 계층에 대한 Standard & Infrequent Access 테이블 클래스가 있습니다
- 각 테이블은 항목이라고 부르는 행을 무한대로 가질 수 있습니다
- 각 항목, 행의 데이터의 최대 크기는 400KB로 제한이 있습니다
- 지원되는 데이터 유형은 스칼라 자료형으로 문자열, 숫자, 이진수, 불리언, null이 있습니다 또는 리스트와 맵도 지원 됩니다.
- 기본 키에는 두 개의 옵션이 있습니다 첫 번째는 파티션 키라고 하는 것으로 HASH 전략이라고도 부릅니다
- ![image](https://github.com/user-attachments/assets/1783a3d0-28f4-40d3-b916-cfb84ebf38df)

- 두 번째 옵션은 파티션 키와 정렬 키로 HASH + RANGE라고도 합니다
- ![image](https://github.com/user-attachments/assets/42781fd0-3a87-428a-bd57-c553be5c4667)

- 항상 카디널리티(cardinality)가 제일 높은 것으로 고르고 가장 많은 값을 가질 수 있는 것을 고르세요 -? (중복도가 낮은것)

# dynamodb read/write capacity modes 
- 프로비저닝 모드는 기본 설정 모드 5개의 쓰기 용량 유닛을 원하면 매시간 비용을 지불하게 됩니다
- 온디맨드 모드는 사전에 계획할 필요가 없습니다. 사용한 만큼만 비용을 지불하며 프로비저닝 모드보다 훨 씬 비쌉니다.
- 목표 사용량을 입력하면 여러분을 위해 DynamoDB가 스케일링해줍니다
- 따라서 WCU와 RCU를 계산하는 공식을 이해하셔야 합니다
- 강력한 일관된 읽기 모드와 최종적 일관된 읽기 모드입니다
- 기본 모드인 최종적 일관된 읽기 모드일 경우 쓰기 후에 읽기를 한다면 오래된 데이터를 얻을 가능성이 있습니다
- 만약 강력한 일관된 읽기 모드라면 쓰기 이후에 데이터를 읽기 원하면 막 쓰인 데이터를 올바르게 읽을 수 있을 것입니다
- 강력한 일관된 읽기 모드는 rcu 를 2번 소비하기 떄문에 비싼 쿼리가 됩니다.
- 즉, 파티션 키만이 해싱 알고리즘을 통해 어느 파티션으로 이동해야 하는지 알 수 있습니다
  - ![image](https://github.com/user-attachments/assets/857f5d6c-a9bc-4c87-8ca1-0b1e630dd840)
- 프로비저닝 처리량 초과 예외를 처리하는 방법
  - 첫 번째, 예외가 발생했을 때 지수 백오프를 하는 것입니다
  - 집중적으로 읽어서 생긴 RCU 문제라면 DynamoDB Accelerator(DAX) 라는 기능을 살펴봐야 합니다
  - 바로 온디맨드 읽기/쓰기 용량 모드입니다 어떤 읽기와 쓰기든 자동으로 승인하여 워크로드에 따라 스케일 업하거나 스케일 다운합니다 -> expensive
# dynamodb writing data
- 이 시험에서는 DynamoDB의 API 호출을 이름으로 참조하는데요
- PutItem을 선택할 경우 기본 키가 같은 새 항목을 만들거나 완전히 교체합니다
- UpdateItem에서는 속성 몇 가지만 편집할 뿐 모든 속성을 편집하진 않아요
- Query가 특정한 파티션 키, 정렬 키라면 항목 Scan은 테이블 전체를 읽습니다
- 전부 삭제하려면 Scan을 수행하지 말고 DeleteTable API를 쓰세요
- 일괄 작업에 속한 모든 작업은 DynamoDB를 통해 병렬로 적용되어 효율성을 높입니다
- 개발자로서든 SQL이 필요하다면 PartiQL을 사용해 DynamoDB에서 SQL을 사용할 수 있습니다

# dynamodb conditional writes
- 조건 표현식이 DynamoDB에 쓰기 작업을 적용할지 여부를 나타낼 조건을 만드는 데 유용하다는 점을 아셨으면 좋겠네요

# dynamodb local secondary index (LSI) OR Global (GSI)
- 다음으로 LSI에서는 메인테이블로부터 일부 또는 전체 속성을 얻을 수 있습니다
- LSI는 테이블에 대체 정렬 키를 제공합니다
- 기본 테이블에 동일한 파티션 키를 가지지만 추가적으로 정렬 키를 얻는 것입니다
- 그래서 테이블 내에 키 속성이 아닌 항목의 쿼리 속도를 높이는데 유용합니다
- GSI 에서는 이렇게 파티션 키와 정렬 키를 정의해서 완전히 다른 새 쿼리를
- 쓰기가 GSI에서 스로틀 되면 메인테이블도 스로틀 됩니다
- 메인테이블에서 WCU에 아무 문제가 없어도 GSI에서 제한이 있으면 메인테이블도 제한됩니다

# dynamodb partiql

# dynamodb - optimistic locking
- DynamoDB에서 조건부 쓰기를 하는 것이죠
- 업데이트하거나 삭제하기 전에 항목이 변경되지 않게 하는 것입니다
- 때문에 다음 요청에서 version 이 바뀐다면 getitem 으로 실행하고 업데이트를 시도해야합니다.

# dynamo db accelerator (dax)
- DAX는 DynamoDB를 위한 완전 관리형, 고가용성, 무결절성 인 메모리 캐시입니다
- 캐시 된 읽기와 쿼리에 마이크로초의 지연 시간이 생깁니다
- 기존 DynamoDB API와 호환됩니다 DAX 클러스터만 생성하면 되죠
- DAX 클러스터는 DynamoDB 테이블에서 직접 데이터를 가져옵니다
- DAX는 완벽하게 안전하며 미사용 시에는 암호화가 됩니다
- DAX로는 쿼리, 스캔이나 혹은 개별 객체에 관한 캐시를 갖게 됩니다
- ![image](https://github.com/user-attachments/assets/60d21fca-9941-4886-a7a1-41dc530abd39)

# dynamodb streams
- 스트림은 테이블에서 발생하는 생성, 업데이트, 삭제와 같은 항목 수준 수정의 정렬된 목록입니다
- DynamoDB Streams를 Kinesis에 보내서 원하는 작업을 할 수 있죠
- DynamoDB 스트림의 데이터 보존 주기는 최대 24시간입니다 그래서 더 오래 계속 보존할 수 있는 Kinesis Data Streams 같은 곳에 보존하거나
- Lambda 또는 KCL 애플리케이션으로 내구성이 좋은 곳에 보존합니다
- DynamoDB 테이블에서 발생하는 실시간 변경 사항에 대응할 때 DynamoDB Streams를 사용합니다
- Kinesis Data Streams가 DynamoDB Streams의 수신기가 될 수 있습니다
- ![image](https://github.com/user-attachments/assets/d067665b-ee99-4279-aa4c-857f96e7d96e)
- 하지만 DynamoDB Streams의 장점은 어떤 샤드도 프로비저닝 하지 않는 것인데이는 AWS에서 자동으로 실행됩니다
- 스트림을 활성화하면 DynamoDB 테이블에 나타나는 변경 사항을 기반으로 한 업데이트를 받게 됩니다
- ![image](https://github.com/user-attachments/assets/be57c98c-6032-4e4c-b647-d3da2fa21af6)


# ttl 
- 타임 투 리브를 사용하면 타임스탬프 만료 이후에 자동으로 아이템을 삭제할 수 있습니다
- 만료 후 48시간 이내에 삭제되도록 보장하고 있습니다 -> 복구 가능
- SessionData는 완벽한 TTL 사용 예시죠

# dynamodb cli
- 첫 번째는 프로젝션 표현식입니다
- 한 개 이상의 속성을 지정해서 가져올 수 있습니다
- 필터 표현식은 반환되는 아이테을 필터링하는 옵션입니다.
- DynamoDB에서는 --page-size입니다
- max-items는 NextToken이나 --starting-token과 결합하여 작동하는데

# dynamodb transactions 
- 트랜잭션을 사용하면 하나 이상 테이블의 여러 아이템에 양단간 작업을 할 수 있게 됩니다.
- DynamoDB에서 최종 일관성 그리고 우리가 아는 강력한 일관성 또 트랜잭션 일관성입니다
- 쓰기 모드 같은 경우는 표준 및 트랜잭션 기능
- 트랜잭션 기능은 필요한 쓰기 및 읽기 용량 단위의 두 배를 소비합니다
- TransactWriteItems는 하나 이상의 PutItem 및 UpdateItem과 DeleteItem을 한 트랜잭션의 일부로 작업합니다
- 예시로 초당 세 번의 트랜잭션 쓰기를 하려는데 아이템 크기는 5KB입니다 WCU는 얼마나 필요할까요? 먼저 3 * (5/1)을 합니다

# dynamodb as session state cache 
- 예를 들어 사용자 로그인 상태를 벡엔드 웹 앱들에 공유할 수도 있죠 이건 DynamoDB의 아주 흔한 활용 사례인데요
- 그럼 시험에서는 세션 상태 저장소가 필요하다고 할 거고요 그건 인메모리가 될 것이라고 하죠, 그럼 ElastiCache를 의미하는 것이죠
- 그리고 만일 오토 스케일링 등을 얘기하면 정답은 DynamoDB일 거예요
- 디스크에도 세션 상태를 저장할 수 있고요 그리고 그 디스크를 다수의 EC2 인스턴스에 걸쳐 공유해야 하죠 그럼 EFS가 훌륭한 선택이 될 수 있어요, EFS를 네트워크 드라이브로서 EC2 인스턴스에 부착해야 해요
- ebs 볼륨과 ec2 인스턴스 -> 공유 캐싱이 불가능합니다. 
- 그리고 마지막으로 S3는 어떨까요? S3를 이용해서 세션 상태를 저장할 수 있을까요? 예, 그렇지만 레이턴시가 크고요, 작은 객체가 아니라 큰 파일에 사용하기 위한 것이죠

# dynamodb write sharding
- 정말 잘 분배된 파티션 키를 얻는 것이 핵심입니다
- 무작위 접미사를 사용하거나 해싱(hashing) 알고리즘을 사용해 계산할 수도 있습니다

# dynamodb write types
- 동시 쓰기는 좋은 방법이 아님 한명만 성공 -> 낙관적 잠금
- 두번째 원자적 쓰기는 모두가 성공하는 방법 
- 마지막으로 동시성과는 아무 관련이 없지만 많은 아이템에 쓰기와 업데이트를 동시에 하는 것이 배치 쓰기입니다

# s3 에서 dynamodb 사용방법
- 큰 객체를 dynamodb(400kb 제한) 가 아닌 s3 에 저장하는것이 좋음
- DynamoDB는 작은 객체 저장에 뛰어나니 특정 속성에 따라 연동될 수 있는 겁니다
- DynamoDB를 인덱스 S3 객체 메타데이터 방식으로 사용하는 것입니다
- ![image](https://github.com/user-attachments/assets/e193b945-d67c-4614-8d73-7fba9617bead)

# dynamo db operations
- 먼저 테이블의 모든 아이템을 스캔 후 하나씩 지우는 방법입니다 굉장히 느리고 스캔 작업에 RCU를 많이 소비하며 삭제 작업에도 WCU를 많이 소모하여 비싼 옵션입니다
- 두 번째는 훨씬 빠른데 테이블을 드롭하는 것입니다 즉 테이블을 드롭, 제거 및 재생성하는 방법입니다 빠르고 효율적이며 저렴하지요
- 만약 DynamoDB 테이블을 계정, 리전, 장소에 걸쳐서 복제하려면 방법은 두 가지입니다
  - data pipeline 사용하는 방법은 백엔드의 Data Pipeline이 Amazon EMR 클러스터를 실행하고 EMR은 스캔 작업을 통해 DynamoDB 테이블에서 읽기를 하고 Amazon S3에 써서 저장합니다
  - ![image](https://github.com/user-attachments/assets/03c9e270-672d-48bb-90b4-d2bf22afda43)

# dynamodb security & other features
- 보안에 관련해서는 VPC 엔드포인트가 있는데요
- DynamoDB로의 액세스는 IAM에 의해 제어되기 때문에
- 그리고 DynamoDB에는 글로벌 테이블이라는 개념이 있습니다 Dynamo DB에는 다수 리전에, 다중 활성화가 가능하며 복제 가능한 고성능의 테이블이 있는데요
- LeadingKey는 기본 키 값을 기반으로 해서 사용자들에게 row 수준의 액세스만을 제한하는 겁니다 이런 식으로 사용자들이 자신의 데이터에만 액세스해서 수정을 할 수 있도록 허용하는 거죠
- 
