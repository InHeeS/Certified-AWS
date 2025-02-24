# cloudfront
- cdn 이 나오면 cloudfront
- 여러 엣지 로케이션에서 웹사이트의 콘텐츠를 캐시 처리해 읽기 성능을 높입니다.
- 콘텐츠를 전 세계에 분산하여 ddos 공격으로부터 보호
- Shield와 Web Application Firewall을 사용하는데요
- CloudFront만 S3 버킷에 액세스하도록 하기 위해 오리진 액세스 제어(OAC)를 사용
- 먼저 버킷을 스태틱 S3 웹사이트로 설정해야 해요
- S3 버킷은 OAC를 사용하고 S3 버킷 정책을 수정해 보안 처리됩니다
- ![image](https://github.com/user-attachments/assets/057f53a2-b5cc-4c7b-ad45-71dd2104093b)
- CloudFront는 글로벌 엣지 네트워크를 사용합니다 약 216개의 POP로 이루어져 있고, 각 엣지 로케이션에서 파일이 하루 정도 캐시 처리
- s3 리전간 복제는 모든 리전에 적용하는 것이 아니라 거의 실시간 업데이트 되므로 캐시 처리가 되지 않고 읽기 전용입니다.
- CloudFront는 CDN으로 전 세계 모든 곳에 콘텐츠를 캐시 처리하는 반면, S3 리전 간 복제는 특정 리전에 전체 버킷을 복제합니다

# Cloudfront caching
- 캐시 키는 호스트 이름과 URl의 리소스 부분으로 구성됩니다
- 캐시 정책에는 ttl 을 적용 가능, 이때 헤더를 이용해 설정을 제어하는데, Cache- Control 헤더와 Expires 헤더가 있습니다
- http 헤더에 적용합니다.
- None을 선택하면 쿼리 문자열이 캐시 키에 사용되지 않고 오리진에 전달되지 않습니다
- Whitelist를 선택하면 포함할 쿼리 문자열을 지정합니다
- 그밖에도 include all-except, all 이 있습니다.
- 반면 캐시 키에 포함하지 않으려면 오리진 요청 정책에서 사용자 지정 HTTP 헤더 또는 CloudFront HTTP 헤더를 오리진에 추가할 수도 있습니다
  -> 오리진 요청 정책으로 사용자 에이전트, 세션id, 참조 쿼리 문자열을 추가해야 합니다. 
- 하지만 캐시는 오리진 요청에 전달되는 내용에 관한 정책이 아니라 캐시 정책을 기반으로 처리됩니다

# Cache invalidations(무효화)
- 캐시 리프레시를 적용하여 모든 ttl 을 제거합니다.
- 파일 경로를 지정하여 * 를 사용하여 모든 데이터에 적용
- cloudfront 에서캐시 무효화를 통해 엣지 로케이션에 전달 오리진(s3 와 같은)에서 새로운 객체 가져옴

# Cache Behaviors
- ![image](https://github.com/user-attachments/assets/1c88a3fb-13a5-4e0f-a130-9f293d98178f)
- Amazon S3에 접근하는 스태틱 요청에 대해서는 캐시 정책에 헤더나 세션을 설정하지 않습니다
- 반면 로드 밸런서나 EC2를 이용하는 REST 또는 HTTP 서버와 같은 동적 콘텐츠에 대해서는 캐시 정책에 정의한 정확한 헤더와 쿠키를 기준으로 캐시 처리합니다
- ![image](https://github.com/user-attachments/assets/03a0bec8-ccb9-4f72-b282-8e3f24327364)

# cloudfront - alb or ec2 as an origin
- CloudFront는 어떠한 사용자 지정 HTTP 백엔드에든 액세스할 수 있고 alb, ec2 포함 됩니다.
- cloudfront 는 프라이빗 vpc 연결이 없기 대문에 모든 퍼블릭 ip 목록을 허용하는 보안 그룹 설정, ec2 는 공개로 설정
- 두번째는 alb 공개하고 ec2 d인스턴스는 비공개로 설정하여 alb 와 ec2 간에 private vpc 연결 되어있음
  - 사용자가 엣지 로케이션에 액세스하고 이 엣지 로케이션의 퍼블릭 IP가 ALB의 보안 그룹에서 허용돼 있으면 연결이 설정될 수 있습니다

# cloudfront geo restriction
- 여러분은 배포판에 접근할 수 있는 사용자를 국가에 따라 제한할 수 있습니다
- GeoIP 데이터베이스를 사용하여 사용자의 IP를 해당 국가에 매핑하는 방식으로 결정됩니다

# cloudfront signed url / signed cookies
- CloudFront 배포를 비공개로 설정해서 전 세계에서 프리미엄 사용자에게만 유료 공유 콘텐츠 액세스를 제공하되, CloudFront 배포에서 어떤 사용자가 어떤 콘텐츠에 액세스 권한이 있는지 확인하려고 합니다
- 엑세스 할 수 있는 ip 범위 지정 및 신뢰할 수 있는 서명자 지정
- 
- 서명된 URL은 개별 파일의 액세스를 제공하므로 파일 하나에 URL이 하나입니다
- 서명된 쿠키는 여러 파일에 대한 액세스를 제공하고 재사용이 가능합니다
- ![image](https://github.com/user-attachments/assets/af1153d5-610d-48e3-bc16-bcc5b488f6f8)
- 
- 즉 `서명된 URL`은 오리진이 S3이든 어떤 http 백엔드이든 상관없이 작동합니다
- CloudFront 서명된 URL은 오리진과 상관없이 경로의 액세스를 허용합니다
- 사용자에게 CloudFront 배포에 대한 액세스를 부여하고 이 배포를 S3 앞에 연결하는 경우 CloudFront 서명된 URL을 사용해야 하는데 버킷 정책에서 OAC를 적용해서 S3 버킷에 액세스할 수 없기 때문이죠

- 반면 사용자가 직접 S3에 액세스하도록 하고 CloudFront를 사용하지 않은 채 S3의 파일을 배포하려는 경우 `미리 서명된 URL`을 사용하는 것이 좋습니다

- 정리
  - ![image](https://github.com/user-attachments/assets/6cf27efc-7427-44e8-951f-fa87253b2746)

# cloudfront signed url process (url 에 서명하기 위한 키 생성 방법)
- API를 활용하여 이러한 키를 생성하고 순환하게 할 수 있으며
- 클라우드 프론트 키 페어를 보유한 계정을 사용하는 것이죠
  - 하지만 이 방법을 통한 키 관리에는 `루트 계정 자격 증명이 필요`하며 AWS 콘솔도 사용을 해야 하는데 이 방법은 `권장되지 않습니다`

---
- 따라서 현재는 신뢰할 수 있는 키 그룹의 사용이 권장됩니다
- 개인키는 개인적으로 보관하여 서명된 url 생성하는데 사용되며 공용 키는 클라우드 프론트로 보낼 부분
- 충분한 권한만 있다면 어떤 IAM 사용자라도 클라우드 프론트 내에서 공용 키와 키 그룹 생성이 가능합니다

# cloudfront pricing 
- 엣지 로케이션에 따른 데이터 전송 비용도 다를 겁니다
- price class 100, 200, all

# cloudfront multi origin
- 고가용성을 증가시키고 한 오리진에서 장애가 발생한 경우 장애 조치가 가능하게 해주죠
- ![image](https://github.com/user-attachments/assets/fa1e901f-df08-4e79-972f-5c3f13311b2d)

# cloudfront filed level encryption
- HTTPS를 사용하는 인플라이트 암호화와 더불어 추가적인 보안을 더해 줍니다
- 전송할 때마다 엣지 로케이션이 이를 암호화하고 개인 키에 대한 권한을 지닌 사용자만이 이 정보를 해독할 수 있도록 하는 개념입니다
- ![image](https://github.com/user-attachments/assets/87309177-a97f-421a-bc92-db92a3fd4075)
  - 오직 웹 서버만이 필드를 해독할 수 있는 커스텀 애플리케이션 논리를 가지고 있죠

# cloudfront real time logs
- CloudFront에서 수신한 모든 요청은 Kinesis 데이터 스트림에 실시간으로 전송할 수 있습니다
- ![image](https://github.com/user-attachments/assets/1d105a70-b569-42e9-b634-0ac2062587f5)
  - firehose 를 통해 s3, opensearch 그 밖에 대상으로 전송합니다.
  - Kinesis 데이터 스트림에서 액세스할 필드나 캐시 동작, 즉 경로 패턴도 지정할 수 있습니다













































