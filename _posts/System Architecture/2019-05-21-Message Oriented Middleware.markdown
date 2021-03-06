---
layout: post
title:  "Message Oriented Middleware"
description: "Message Oriented Middleware"
date:   2019-05-21 19:00:00
categories: System_Architecture
comments: true
---
## 1. Message Oriented Middleware
- ![Message Oriented Middleware](../../assets/System_Architecture/14.PNG)

- 비동기식 메시지 전달에 기초하여 메시지 공급자를 사용하여 메시지 작업을 중재
- 기본 요소 : 메시지, 메시지 공급자(+ 관리 도구), 클라이언트(+ API)
- 메시지 공급자는 서로 다른 구조를 사용하여 메시지를 라우팅하고 전달할 수 있다.
  - 중앙 집중식 메시지 서버
  - 각 클라이언트 컴퓨터에 직접 라우팅 및 전달 기능 배포
  - 일부 미들웨어에서는 두 가지 방식을 결합하여 사용하기도 한다.
- 클라이언트는 메시지 지향 미들웨어 시스템의 API 호출을 통해 메시지 공급자가 관리하는 대상들에게 메시지를 전송한다.

많은 메시지 지향 미들웨어는 메시지 큐 시스템을 기반으로 한다.
그 밖에도 브로드캐스트 형식의 메시지 시스템, 멀티 캐스트 형식의 메시지 시스템 등이 있다.

### (1) 장점
메시지를 전달하는 과정에서 메시지를 보관하거나, 라우팅 및 변환할 수 있다.
- 저장 : 많은 메시지 지향 미들웨어는 전송되는 메시지의 백업 데이터를 유지함으로써 지속성을 제공한다.
  - 때문에, 송신 측과 수신 측이 동시에 네트워크에 연결되어 있을 필요는 없다.
  - 수신 측에서 문제가 발생하여 응용 프로그램이 중지되어도 송신 측은 이에 영향을 받지 않고 계속해서 전송할 수 있고, 이후에 수신 응용 프로그램이 재개된 후 저장된 메시지를 처리한다.
- 라우팅 : 미들웨어 계층에서 직접 메시지 라우팅이 가능하다.
  - 메시지 공급자가 메시지를 전달하는 것 뿐만 아니라, 하나의 메시지를 직접 여러 수신자에게 배포 (멀티캐스트)
- 변환 : 송신 측과 수신 측의 요구에 따라 메시지의 변환이 가능하다.
  - 송신 시, 수신 시 각각의 규칙을 정하여 사용자가 메시지를 보내거나 받는 경우에 규칙에 따라 메시지를 변환하여 보내거나 받을 수 있다.

### (2) 단점
- 메시지 지향 시스템 아키텍쳐에 외부 구성 요소인 메시지 전송 에이전트를 필요로 한다는 것이 주요 단점이다.
  - 일반적으로 새로운 구성 요소를 추가하면 전체적인 시스템 성능이 저하되고, 신뢰성도 떨어진다. 또한, 전체 시스템에 대한 관리가 더 어려워지고, 관리 비용도 증가한다.
  - 게다가, 응용 프로그램 간의 통신은 본질적으로 동기 방식이며, 전송 처리를 계속하기 전에 응답을 기다리도록 되어 있다. 하지만, 메시지 기반 통신은 본질적으로 비동기 방식이며, 불일치가 발생할 수 있다. >> 이는 많은 메시지 기반 미들웨어에서 요청을 그룹화하여 하나의 의사 동기 트랜잭션으로 응답하는 기능으로 해결하고 있다.
- 표준의 부족 : 표준이라고 부를 정해진 규격이 없다. 기업 및 기관들은 자체적으로 구현하고, 자체 API와 관리 도구를 갖고 있거나 Java EE 환경에서 제공되는 JMS(Java Message Service) 등 엔터프라이즈급 환경에서 제공하는 표준 API를 사용한다.
