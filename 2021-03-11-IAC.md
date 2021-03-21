---
layout: post
title:  "Infrastructure as Code"
date:   2021-03-11 21:03:36 +0530
categories: Infrastructure as Code Cloud
---

데브옵스에서 소스제어와 자동화는 매우 중요한 요소입니다. 클라우드 환경에서는 모든 것이 추상화되고, 네트워크 토폴로지와 시스템 구성을 코드로 표현하는 것은 쉽게 상상할 수 있습니다. 이를 위해서는 대표적으로 IaC라고 불리는 Infrastructure as Code라는 개념이 있습니다. IaC를 이용해서 로드벨런서, 서버, 네트워크등의 리소스를 다룰 수 있습니다. 두번째는 구성 관리(Configuration)시스템이 있습니다. 이를 통해 관리자는 패키지 설치나 앱 배포까지 자동화할 수 있습니다. 
<br/>

## 클라우드 포메이션
[cloudformation][cloudformation]은 애플리케이션을 시작시키는 일련의 AWS 리소스와 구성 정보를 기술합니다. 이를 각각의 스택을 통해서 기술하며, 이를 통해서 리소스를 개별적으로 관리하는 대신 템플릿을 통해 전체 스택을 단일 단위로 처리하여 필요한 만큼 자주 생성 및 업데이트하고 삭제할 수 있습니다. 이러한 템플릿을 작성하는 방법에는 두가지가 있습니다. 

1. AWS 클라우드 포메이션 디자이너
AWS 클라우드 포메이션 디자이너는 GUI를 통해서 템플릿을 작성하는 도구입니다. 메뉴를 통해 캔버스로 리소스를 끌어다 놓기만 하면, 간단하게 리소스를 추가할 수 있습니다.
<img width="1437" alt="스크린샷 2021-03-11 20 49 41" src="https://user-images.githubusercontent.com/45285053/110783464-d33de700-82ab-11eb-8913-b913e5d83ebf.png">
<br/>
2. 클라우드 포머
클라우드 포머는 이미 존재하는 리소스로 클라우드 포메이션 템플릿을 생성하는 도구입니다. 임의로 생성한 일련의 리소스가 있다면, 클라우드 포머를 이용해 새로운 클라우드 포메이션 템플릿을 추출할 수 있습니다. 

## Hello "클라우드 포메이션"
데브옵스의 관점에서 클라우드 포메이션의 가장 강력한 기능은 탬플릿을 동적으로 생성할 수 있는 것 입니다. 파이썬과 Troposphere 라이브러리를 통해서 이를 구현할 수 있습니다. 

```javascript
# 여러가지 definition import
from troposphere import (
    Base64,
    ec2,
    GetAtt,
    Join,
    Output,
    Parameter,
    Ref,
    Template,
)

ApplicationPort = "3000"
# 템플릿 변수 초기화
t = Template()

# 스택을 설명할 수 있는 설명
t.add_description("Effective DevOps in AWS: HelloWorld web application")

t.add_parameter(Parameter(
    "KeyPair",
    Description="Name of an existing EC2 KeyPair to SSH",
    Type="AWS::EC2::KeyPair::KeyName",
    ConstraintDescription="must be the name of an existing EC2 KeyPair.",
))

# 보안 그룹 정의 TCP 3000, SSH 22
t.add_resource(ec2.SecurityGroup(
    "SecurityGroup",
    GroupDescription="Allow SSH and TCP/{} access".format(ApplicationPort),
    SecurityGroupIngress=[
        ec2.SecurityGroupRule(
            IpProtocol="tcp",
            FromPort="22",
            ToPort="22",
            CidrIp="0.0.0.0/0",
        ),
        ec2.SecurityGroupRule(
            IpProtocol="tcp",
            FromPort=ApplicationPort,
            ToPort=ApplicationPort,
            CidrIp="0.0.0.0/0",
        ),
    ],
))

## 사용하는 AMI에 따라 수정해야함
ud = Base64(Join('\n', [
    "#!/bin/bash",
    "sudo yum install --enablerepo=epel -y nodejs",
    "wget http://bit.ly/2vESNuc -O /home/ec2-user/helloworld.js",
    "wget http://bit.ly/2vVvT18 -O /etc/init/helloworld.conf",
    "sudo start helloworld"
]))

t.add_resource(ec2.Instance(
    "instance",
    ImageId="ami-a4c7edb2", //수정해야함
    InstanceType="t2.micro",
    SecurityGroups=[Ref("SecurityGroup")],
    KeyName=Ref("KeyPair"),
    UserData=ud,
))

t.add_output(Output(
    "InstancePublicIp",
    Description="Public IP of our instance.",
    Value=GetAtt("instance", "PublicIp"),
))

t.add_output(Output(
    "WebUrl",
    Description="Application endpoint",
    Value=Join("", [
        "http://", GetAtt("instance", "PublicDnsName"),
        ":", ApplicationPort
    ]),
))

print t.to_json()
```
위와 같은 스크립트를 통해서 클라우드 템플릿을 생성하고, 탬플릿을 통해 클라우드 포메이션 스택을 띄울 수 있습니다.


[cloudformation]: https://aws.amazon.com/ko/cloudformation/
