# cloudformation
- CloudFormation은 코드를 사용하여 AWS 인프라의 모든 리소스를 정의하는 방법입니다
- Git 등을 사용하여 버전 제어에 사용될 수 있습니다
- 필요한 것을 생성하고 삭제하며 사용한 만큼만 비용을 지불하는 방식입니다
- 그리고 코드에는 인프라를 구성하려는 선언적 방식이 포함됩니다 그런 다음 Application Composer를 사용하여 이 인프라를 시각화하여

# cloudformation works
- 템플릿을 아마존 S3에 업로드한 다음 CloudFormation에서 참조해야 합니다
- 업로드한 다음에 CloudFormation에서 이를 참조하면 스택이 생성됩니다
- stack 은 AWS 리소스로 구성되어 있으며 AWS에서 생성할 수 있는 모든 종류의 것이 될 수 있습니다
- CloudFormation 스택을 삭제하면 CloudFormation에서 생성된 모든 단일 아티팩트와 리소스가 삭제됩니다
- 명령줄 인터페이스나 지속적 전달 도구를 사용하여 템플릿을 배포할 수 있습니다
- 파라미터(Parameters)는 템플릿에 대한 동적 입력이고 매핑(Mappings)은 템플릿에 대한 정적 변수입니다
---
- 이 코스와 CloudFormation에서 가장 자주 보게 될 것은 YAML 템플릿이 될 것이라고 생각합니다

# cloudformation - resources
- 앞서 말했듯이 리소스는 CloudFormation 템플릿의 핵심입니다
- 따라서 리소스 유형 식별자는 서비스 공급자 뒤에 콜론이 2개 붙고 서비스 이름 뒤에 콜론이 2개 붙고 데이터 유형 이름이 나오는 형식이 됩니다
- 시험에서 여러분이 이를 우회할 수 있는 방법을 묻는다면 CloudFormation 사용자 정의 리소스를 사용하는 것입니다

# cloudformation - parameters
- 파라미터는 CloudFormation 템플릿에 입력을 제공하는 방법입니다
- 따라서 파라미터는 매우 강력하고 제어될 수 있으며 유형을 활용하여 템플릿에서 오류가 발생하는 것을 방지할 수도 있습니다
- 파라미터가 단순히 문자열만 있는 게 아니라는 것만 기억해 주세요
- 시험과 관련도니 값으로 허용되는 값, noecho, 가상 파라미터(default value)
  - 가상 파라미터 예시로 사용자가 실제로 us-east-1에 있다는 것을 CloudFormation 템플릿에 알려줄 필요가 없습니다

# cloudformation -mappings
- 매핑은 CloudFormation 템플릿 내의 고정 변수입니다
- 그리고 서로 다른 환경을 구별하려는 경우 매우 편리합니다
- ![image](https://github.com/user-attachments/assets/3890c950-052f-40e4-ae73-61bb08af48e2)
- 매핑은 리전, 가용 영역, AWS 계정, 환경(개발 대 프로덕션) 등과 같은 변수에서 도출할 수 있는 모든 값을 미리 알고 있을 때 적합합니다
  - 실제로 달라지는 값이 있는 경우사용자에게 최대 자유도를 제공하기 위해 파라미터를 사용합니다

# cloudformation - outpus
- 출력 섹션은 선택 사항입니다
- 예를 들어 네트워크 CloudFormation 스택을 정의하고 VPC ID와 서브넷 ID를 출력하여 다른 방법으로 재사용하는 경우출력이 있으면 매우 유용합니다
- 로 연결되어 있기 때문에 다른 모든 스택이 더 이상 참조하지 않을 때까지 해당 값을 내보낸 스택을 삭제할 수 없습니다

# cloudformation - conditions
- 시험을 치기 위해서는 조건 작성 방법을 알 필요가 없습니다
- 너무 고급 단계이기 때문인데, 필요할 때 조건이 있다는 것 정도는 알아야 합니다

# cloudformation - intrinsic functions (내장 함수)
- Ref, GetAtt, FindInMap, ImportValue, 조건 함수는 If, Not, Equals 등이 있습니다, Base64
  - ref : 참조를 얻기 위해 활용, 파라미터의 값을 반환하기 위하거나 물리적 id 반환하는 리소스에 대한 참조
  - getatt : 함수의 속성 값을 얻기 위한 것
    - ID, 프라이빗 DNS 이름, 프라이빗 IP, 퍼블릭 DNS 이름 및 퍼블릭 IP를 얻을 수 있습니다
  - FindInMap : 특정 맵의 특정 키에서 직접 값을 가져오는 것
  - ImportValue : 다른 스택에서 내보낸 값을 가져오는데 사용
  - 문자열을 Base64 표현으로 변환하는 Base64 함수가 있습니다
    - 데이터를 EC2 인스턴스의 사용자 데이터에 전달하기 위해서입니다
  - 조건부 함수 : 조건부로 리소스를 생성하는 등의 조건을 거는 함수입니다

# cloudformation roolback (중요!!!)
- 스택을 생성했는데 스택 생성이 실패하는 경우 두 가지 옵션이 있습니다
- 스택 업데이트에 문제가 있는 경우 기본적으로 스택은 기본 옵션으로 모든 것이 롤백되어 삭제된다는 의미입니다
  - 프로비저닝된 리소스를 성공적으로 유지하는 것을 선택합니다 -> 전체가 롤백되지 않는다. 
- 생성 로그를 보면 무슨 일이 있는지 이해하며 리소스는 볼 수 없습니다.
- 롤백을 했지만 도중에 오류가 발생한다면 그렇다면 이것은 스택에 문제가 있는 것입니다
  - 수동으로 리소스를 수정해야 합니다. 업데이트 롤백 계속 (ContinueUpdateRollback)이라는 것을 실행하여 CloudFormation에 롤백을 다시 시도하도록 지시할 수 있습니다

# cloudformation -service role
- 사용자가 생성하는 CloudFormation 전용 IAM 역할입니다.
  - 해당역할은 스택에 적용도비니다. 
- 또한 사용자를 대신하여 CloudFormation이 스택 리소스를 실제로 생성, 업데이트 및 삭제할 수 있게끔 합니다.
- 리소스로 직접 작업할 권한이 없는 경우 서비스 역할을 사용하면 됩니다.
- ![image](https://github.com/user-attachments/assets/51487f01-cc04-4396-b567-b1cccf39e8a6)

- CloudFormation에서 작업을 수행할 수 있는 자체 IAM 권한을 정의합니다.

# cloudformation - capabilities 
- CAPABILITY_NAMED_IAM과 CAPABILITY_IAM이 있습니다.
- CloudFormation 템플릿을 통해 IAM 리소스를 만들거나 업데이트할 때마다 CloudFormation에 제공해야 하는 capabilities 입니다.
- 따라서 리소스에 이름이 있는 경우 CAPABILITY_NAMED_IAM을 특정하면 됩니다.
- CAPABILITY_AUTO_EXPAND는 동적 변환을 수행, 또는 스택 내의 스택을 포함된 경우필수적입니다.
- 마지막으로 템플릿을 런칭하는 중에 InsufficientCapabilitiesException을 획득한 경우,CloudFormation 템플릿에 capabilities 이 필요했지만 이를 확인해주지 않았기 때문에 발생하므로,
  - 때문에 보안 조치로 템플릿 업로드를 다시 실행하며 capabilities 를 런칭해야 합니다.

-  CloudFormation 이 사용자 지정 이름으로 IAM 리소스를 생성할 수 있다는 사실을 확인합니다
  - 이걸 체크하면 이 템플릿을 실행하고 이 역할을 만들 수 있도록 CloudFormation에 capability 을 제공하게 되는 것입니다.

# cloudformation deletetion policy 
- 삭제 정책은 CloudFormation 템플릿의 리소스에 적용할 수 있는 설정으로
- CloudFormation 템플릿에서 리소스가 제거되거나 CloudFormation 스택이 삭제될 때 리소스에 발생할 일을 컨트롤할 수 있게 해줍니다.
- S3 버킷이 자동으로 사라지기 전에 실제로 S3 버킷 내의 모든 항목을 삭제하는 사용자 지정 리소스를 구현하는 것입니다.
- dynamodb 의 경우에는 테입르은 유지되고 몯느 리소스에서 작동
  - Deletionpolicy : Retain
- 삭제 정책으로 스냅샷이 있습니다. EBS 볼륨과 ElastiCache Cluster와 ElastiCache ReplicationGroup, RDS DBInstance, RDS DBCluster, Redshif Cluster, Neptune DBCluster, DocumentDB DBCluser
  - Deletionpolicy : Snapshot
 
# cloudformation stack policies
- 기본적으로 CloudFormation 스택 업데이트가 있으면 어떤 작업이든 모든 리소스에서 허용됩니다
- 스택 또는 스택의 일부를 업데이트로부터 보호하고 싶을 수 있습니다 이럴 때 스택 정책을 사용하면 됩니다
- ![image](https://github.com/user-attachments/assets/a1c11053-3643-40bb-970d-110a38cbb066)
- 스택 정책의 목표는 의도치 않은 업데이트로부터 리소스를 보호하는 것입니다

# cloudformation termination protection
- 이 스택을 삭제하려면 먼저 종료 방지를 수정해서 비활성화해야 합니다

# cloudformation custom resources
- 사용자 지정 리소스를 사용하면 아직 CloudFormation에서 지원되지 않는 리소스를 정의하거나,
- 자체 온프레미스 리소스 또는 타사 리소스와 같이 CloudFormation 외부에 있을 수 있는 리소스에 대한 사용자 지정 프로비저닝 로직을 정의할 수 있습니다.
- 또는 Lambda 함수를 통해 CloudFormation 스택의 생성, 업데이트, 삭제 단계에서 사용자 지정 스크립트가 실행되도록 할때도 사용할 수 있습니다.
  - 삭제되기 전에 람다 함수를 실행하여 S3 버킷을 비우는 것입니다.
- 그리고 서비스 토큰은 람다 함수 ARN 또는 SNS ARN 입니다. 같은 지역에 있어야 합니다.
  - 그리고 이 람다 함수는 사용자 정의 리소스를 프로비저닝하거나
- 사용사례로는 S3 버킷에서 콘텐츠를 삭제하는 경우입니다.
  - 이를 위해 사용자 지정 리소스를 사용하고, 해당 사용자 지정 리소스가 삭제되면 API 호출을 실행하여 실제로 S3 버킷을 삭제하는 방식입니다.
  - ![image](https://github.com/user-attachments/assets/a353208a-b8f8-4598-9af4-8ef6543775c8)

# cloudformation stacksets
- StackSets은 단일 작업 또는 템플릿 내에서 여러 계정과 지역에 걸쳐 스택을 생성, 업데이트 또는 삭제할 수 있습니다.
- 또한 이 스택 세트를 사용하면 여러 지역의 여러 계정에 스택을 배포할 수 있습니다.
- 스택을 업데이트할 때 모든 대상 계정과 모든 대상 지역에 있는 모든 스택 인스턴스도 업데이트되도록 설정하세요
