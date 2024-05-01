# CloudFront

*시험에서 cdn 을 본다면 cloudfront를 떠올리면 됩니다*
웹사이트의 컨텐츠를 서로 다른 엣지 로케이션에 미리 캐싱하여 읽기 성능을 높이는 것입니다. 
전세계의 사용자들이 낮은 레이턴시로 접근할 수 있어서 사용자 경험을 증대시킬 수 있습니다. 
cloudfront 는 ddos 로 부터 보호 가능합니다. 

cloudfront의 원본 제공 방식은 s3버킷으로 cloudfront 를 통해 파일을 분산하고 캐싱할 수 있게 합니다. 
그리고 버킷에는 cloudfront만 접근 가능하게 보장합니다.즉, origin access control 을 대체합니다. 
ingress 는 cloudfront를 통해 버킷에 데이터를 보내는 방법도 가느합니다. 

![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/4b328a1f-49b9-4832-929c-11bc415f427c)

이렇게 cloudfront 와 엣지 로케이션을 이요해 특정 리전에 속한 s3 버킷을 전세계의 엣지 로케이션으로 분산 가능하게 합니다. 

cloudfront vs crr(교차 리전 복제)

cloudfront는 전세계으 엣지 네트워크를 이용합니다. 정적 컨텐츠를 사용하고자 할 때 용이합니다. 

교차 리전 복제는 복제를 원하는 각리전에 이 설정이 되어 있어야 합니다. 전세계 대상이아니며 파일을 거의 실시간을 갱신 됩니다 캐싱되지않고 읽기 전용 설정만 가능합니다. 동적 컨텐츠를 낮은 지연 시간으로 제공하고자 할대 유용합니다. 

정리하자면 cloudfront는 전세계에 걸친 컨텐츠 전송 네트워크이며, 반면 s3 교차리전 복제는 다른 리전으로의 버킷 복제입니다. 

# CloudFront alb or ec2 as an origin

cloudfront는 사용자 지정 http 백엔드에도 접근 가능합니다. 여기에는 ec2 인스턴스나 alb도 포함됩니다. 
