# DNS
- DNS는 인터넷의 중추로 URL과 호스트 이름을 IP로 변환하는 것을 여러분이 이해할 수 있는 방법
- DNS 레코드에는 여러 종류 A, AAA, CNAME, NS
- 최상위 도메인으로는 .com, .us, .in, .gov, .org 등이 있어요 2단계 도메인은 amazon.com, google.com입니다
- 루트 DNS 서버는 본 적 없어요, 그러나 .com은 알고 있습니다'라고 말합니다 .com은 이름 서버(NS) 레코드로 공인 IP 1.2.3.4로 가보라고 알려줍니다 -> 답에 가까운 곳을 알려준다 (NS)
- DNS 서버에는 example.com에 대한 항목이 있습니다 'example.com이 무엇인지 알고 있죠'라고 하고 example.com은 A 레코드이고 이것의 결과로 IP 9.10.11.12를 얻습니다 -> 5.6.7.8 ... 질문 후에 최종 DNS 서벌르 순차적을 찾는다.

# Route 53
- DNS 레코드를 아마존 Route 53의 호스팅 존에 쓰려고 합니다
- 100% SLA 가용성을 제공하는 유일한 AWS 서비스입니다
- 예를 들어 123.456.789.123인데 라우팅 정책은 Route 53이 쿼리에 응답하는 방식입니다
- ttl, 레코드가 캐싱 되는 시간

# A
- 호스트 이름과 ipv4 ip 매핑
- example.com -> 1.2.3.4

# AAAA
- 호스트 이름을 ipv6 ip 매핑

# CNAME
- 호스트 이름을 다른 호스트 이름과 매핑
- 대상 호스트 이름은 A, AAAA 가 될 수 있음
- Route 53에서 DNS 이름 공간 또는 Zone Apex의 상위 노드에 대한 CNAMES를 생성할 수 없습니다
- www.example.com에 대한 CNAME 레코드는 만들 수 있습니다, example.com 은 만들 수 없습니다.

# NS
- 호스팅 존의 이름 서버 입니다.
- 서버의 DNS 이름 또는 IP 주소로 호스팅 존에 대한 DNS 쿼리에 응답할 수 있습니다

# Hosted Zones
- 프라이빗 호스팅 존
  - vpc 만이 url 을 리졸브 가능합니다.
  - 프라이빗 호스팅 존은 프라이빗 IP를 알려줍니다
- 퍼플릭 호스팅 존
  - 공개된 클라이언트로부터 온 쿼리에 응답 가능합니다.

# CNAME vs Alias
- CNAME
  - app.mydomain.com이 blabla.anything.com으로 향하는 식이죠
  - 이건 루트 도메인 이름이 아닌 경우에만 가능해서 mydomain.com 앞에 뭔가 붙어야 하죠
 
# Alias
  - 별칭 레코드는 루트 및 비루트 도메인 모두에 작동합니다
  - `aws resource 로 향할 수 있기 때문에 유용합니다.`
  - 만약 기반 ALB에서 IP가 바뀌면 별칭 레코드는 이걸 바로 인식할 겁니다
  - 별칭 레코드는 Zone Apex라는 DNS 네임스페이스의 상위 노드로 사용될 수 있습니다
  - ttl 를 적용할 수 없습니다.
  - 별칭 레코드의 대상은 elb, cloudfront, api gtateway, beanstlak, s3, vpc interface endpoints, global accelerator, route 53 record
  - EC2 DNS 이름은 별칭 레코드의 대상이 될 수 없습니다

# Record policies
- 라우팅 정책은 Route 53가 DNS 쿼리에 응답하는 것을 돕습니다
- DNS는 트래픽을 라우팅하지 않죠 트래픽은 DNS를 통과하지 않아요
- DNS는 DNS 쿼리에만 응답하게 되고 클라이언트들은 이를 통해 HTTP 쿼리 등을 어떻게 처리해야 하는지를 알 수 있게 되는 거죠
- 단순, 가중치 기반, 장애 조치 지연 시간 기반, 지리적, 다중 값 응답, 지리 근접 라우팅 정책이 있죠

# 단순 라우팅 정책
  - foo.example.com으로 가고자 한다고 하면 Route 53이 IP 주소를 알려주는 거죠
    - A record
  - 동일한 레코드에 여러 개의 값을 지정하는 것도 가능한데요
  - 그렇기 때문에 상태 확인은 할 수 없습니다
 
# 기반 라우팅 정책 
