---
layout: post
title:  "What is DevOps"
date:   2021-03-10 21:11:36 +0530
categories: DevOps
---

데브옵스(DevOps)는 소프트웨어의 개발(Development)과 운영(Operations)를 의미합니다. 데브옵스는 소프트웨어 개발조직과 운영조직간의 상호 의존적 대응이며 조직이 소프트웨어 제품과 서비스를 빠른 시간에 개발 및 배포하는 것을 목적으로 하며, 이러한 데브옵스가 등장한 배경으로는 개발자와 운영자가 함께 일하기 위한 문화가 등장함에 있습니다. 

<br/>
소프트웨어 개발 방법론이 기존의 폭포수기반의 방법론에서 빠르게 변화하는 환경에 적응하기위해 에자일 방법론이 등장하고 이러한 변화가 QA, 개발자, 디자이너들과 함께 협업하는 것이 데브옵스의 시작이였습니다. 
데브옵스는 현재 조직의 의사소통 구조를 반영할 수 있는 문화입니다. 데브옵스 문화의 채택은 전통적인 엔지니어링 팀와 운영팀 전체를 수렴하는 절차와 도구를 이용하는 모든 것을 의미합니다. 

## 데브옵스 핵심원리
이러한 데브옵스는 몇 가지 핵심 특징이 있습니다. 
<ol>
  <li>모든 것을 소스로 제어하기</li>
  <li>자동화된 테스트</li>
  <li>인프라 프로비저닝 및 자동화</li>
  <li>배포 자동화</li>
  <li>모든 것을 측정하기</li>
</ol>

#### 모든 것을 소스로 제어하기
데브옵스의 실천을 위해서는 제품의 소스코드에 대한 버전관리가 필요합니다. 소스코드 관리자를 통해서 코드에 대한 정기적인 리뷰가 이루어져야 합니다. 

#### 자동화된 테스트
소프트웨어의 검증을 위해서 네 가지 단계의 테스트 자동화는 필수적입니다. 
- 단위테스트
- 통합테스트
- 사용자 인터페이스 테스트
- 시스템 테스트

#### 인프라 프로비저닝 및 자동화
평균적인 인프라의 크기와 복잡도가 매우 증가해왔습니다. 데브옵스는 인프라를 관리하기 위해서 일반적으로 모두 자동화합니다. 
아래와 같이 테라폼을 통해서 인프라를 제어할 수도 있습니다. 
<a href='https://www.terraform.io/'>https://www.terraform.io/<a>
  
```javascript
resource "aws_instance" "iac_in_action" {
  ami               = var.ami_id
  instance_type     = var.instance_type
  availability_zone = var.availability_zone

  // dynamically retrieve SSH Key Name
  key_name = aws_key_pair.iac_in_action.key_name

  // dynamically set Security Group ID (firewall)
  vpc_security_group_ids = [aws_security_group.iac_in_action.id]

  tags = {
    Name = "Terraform-managed EC2 Instance for IaC in Action"
  }
}
```

#### 배포 자동화
빠르게 변화하는 환경에 맞추기 위해 자동화된 CI/CD 파이프라인을 갖추어야 합니다. 소프트웨어의 빌드결과를 무사히 상용화하는 것은 중요합니다.

#### 모든 것을 측정하기
제품 및 제품을 개발하는 팀은 측정 지표를 사용해서 품질을 평가할 수 있어야 합니다.

## 데브옵스 Infrastructure as Code
"프로그래밍형 인프라"라고도 하는 Infrastructure as Code는 인프라 구성을 마치 소프트웨어를 프로그래밍하는 것처럼 처리하는 방식을 가리킵니다. 
스크립트를 통해서 인프라를 동작시키고 오케스트레이션을 하는 것은 데브옵스에서 중요한 요소입니다. 

MSA를 위한 컨테이너 개념 혹은 인프라 프로비저닝을 위한 테라폼이 역시 코드로써의 인프라에 해당합니다. 

클라우드가 발전함에 따라 사용자들은 데브옵스에 필요한 핵심적인 도구와 서비스를 제공받을 수 있습니다.


