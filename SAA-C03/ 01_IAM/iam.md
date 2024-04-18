# IAM

- 사용자 추가
  
  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/0f002d95-ffc1-442a-81b1-24233ba64d2c)
  
- 그룹 생성
  
  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/d9ed3777-440c-4a8a-8ecf-cf58b6cdc0a0)
  
- 태그 생성
  
  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/9f208885-65f3-4ec5-8523-12442dc33801)

- 사용자 URL 및 별칭 추가
  
  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/fe972bd0-67f8-400a-84d6-3299b6347009)

# IAM 정책

- 정책 추가

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/3896a633-9770-41e0-9938-2685140577de)

- Custom Policy JSON

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/19d63e79-4816-44ce-bc68-9cb1ddc8eb9e)

# IAM MFA

- 암호 정책 편집

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/6f90e88c-eb86-42e2-a870-0e4da2b32483)

- 멀티 팩터 인증

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/34175248-01fc-4afe-a1fc-0eee7b75f466)

- CLI Access Key

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/e25a6859-0f0d-4f04-a5ba-21690fa8bf9b)

- CloudShell

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/06c9ec02-be18-4ca5-83eb-f4b4c04836a3)

# IAM Role   

- 정책 생성

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/1dfb4619-9eaf-47e1-bae2-4c25ca08a29b)

# IAM 보안 도구 

- 액세스 관리자

  ![image](https://github.com/InHeeS/Certified-AWS/assets/105423951/9c26da2f-a029-4180-83e3-16cfd040d8fa)


# IAM 가이드라인 및 모범 사례

root user 의 자격증명을 주지 말고 새로운 사용자를 만들어 주세요.

사용자를 그룹에 넣어 해당 그룹에 권한을 부여합니다.

AWS 서비스에 권한을 부여할 때마다 역할을 만들고 사용해야 합니다. 

AWS 프로그래밍 할 경우 즉, CLI나 SDK 를 사용할 경우 반드시 액세스 키를 생성해야 합니다. 

계정의 권한을 감사할 때는 IAM 자격 증명 보고서와 IAM 액세스 분석기를 사용합니다. 

IAM 사용자와 액세스 키를 공유하지 않습니다. 

# IAM 요약

IAM 사용자는 AWS 콜솔 비밀번호를 가집니다.

사용자 도는 그룹에 대한 권한을 설명하는 정책 도는 JSON 문서를 첨부할 수 있습니다. 

역할은 ID가 될 거지만, 이번에는 EC2 인스턴스나 기타 AWS 서비스에 대한 것이 될것이다. 

또한 보안을 위해 멀티팩터 인증 및 MFA를 활성화 합니다.

사용자에 대한 비밀번호 정책도 설정합니다

IAM 사용량을 감사할 수 있습니다. IAM 자격 증명 보고서를 생성하고 IAM 엑세스 어드바이저 서비스를 이용하세요 

# IAM QUIZ 

1. (참/거짓) IAM 사용자 그룹은 IAM 사용자 및 기타 사용자 그룹을 포함할 수 있습니다. (X)

2. IAM 정책의 문장은 시드, 효과, 원칙, 조치, 리소스, 그리고 조건으로 구성됩니다. 버전은 IAM 정책 자체의 일부이지, 문장의 일부가 아닙니다.



