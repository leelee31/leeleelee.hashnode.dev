---
title: "kafka 기본 개념"
datePublished: Thu Jul 18 2024 14:23:10 GMT+0000 (Coordinated Universal Time)
cuid: clyrd2n4w000n0al18kj9cz7v
slug: kafka
tags: kafka

---

  
  
개발 공부할 때 기술이 생겨난 배경을 다루는 1장이 제일 재밌다.

인프런 강의를 들으며 나름대로 이해한 내용을 블로그에 정리한다.

  
  

# **섹션 1. 아파치 카프카의 역사와 미래**

세상이 복잡해지면서 데이터와 데이터 연결 관계도 복잡해졌다.

데이터를 생성하는 소스 애플리케이션과 데이터가 적재되는 타깃 애플리케이션을 연결하던 구성은 세상의 복잡성을 다루기 힘들어졌다.

이를 개선하기 위해 프로듀서/컨슈머 개념을 이용하여 데이터를 중앙화하였다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1721308168131/c706c679-0910-4681-bd63-e2a51e7e4156.png align="center")

* 토픽은 테이블과 비슷하다. 특정한 주제로 구분되는 데이터 모음이다
    
* 프로듀서가 메세지를 발행하면 토픽의 파티션에 들어간다
    
* 파티션은 큐 자료구조와 비슷하다
    
* 컨슈머는 토픽의 앞부분부터 소비한다
    
* 큐와 달리 컨슈머가 소비한 즉시 제거되는 것은 아니다
    
* 컨슈머가 어디까지 읽었는지 기록하는데 이를 커밋이라 한다
    

  
  

## 아파치 카프카가 데이터 파이프라인으로 적합한 4가지 이유

1. 높은 처리량
    

* 프로듀서가 브로커로 데이터를 보낼 때, 컨슈머가 브로커로부터 데이터를 받을 때 묶어서 전송한다
    
    * 많은 양의 데이터를 송수신할 때 맺어지는 네트워크 비용은 무시할 수 없다
        
    * **동일한 양의 데이터를 보낼 때 네트워크 통신 횟수를 최소한으로 줄인다면 동일 시간 내에 더 많은 데이터를 전송할 수 있다**
        
    * 대용량의 실시간 로그데이터 처리하는 데에 적합하다
        
    * 프로듀서에는 메세지를 배치로 묶어 보내는 옵션이 있다
        
* 동일 주제의 데이터를 여러 파티션에 분배하여 데이터를 병렬처리할 수 있다
    
    * 컨슈머와 파티션 개수를 추가하면 병렬 처리되어 전체 처리량이 늘어난다 (scale-out)
        

  
2\. 확장성

* 하루는 1,000건 하루는 100만 건이 들어와도 카프카는 안정적으로 확장 가능하도록 설계되었다
    
* 데이터가 적을 때는 브로커 개수를 적게, 데이터가 많을 때는 개수를 늘린다
    
* scale-in, scale-out 과정은 무중단 운영을 지원하므로 24시간 데이터를 처리해야 하는 커머스나 은행 같은 비즈니스 모델에 적합하다
    

  
3\. 영속성

* 영속성이란?
    
    * 데이터를 생성한 프로그램이 종료되더라도 사라지지 않는 데이터의 특성
        
* 다른 메시징 플랫폼과의 달리 전송받은 데이터를 메모리에 저장하지 않고 **파일 시스템**에 저장
    
    * 메모리 저장과 파일 시스템 저장이 뭐가 달라?
        
    * 바로 AI한테 물어봤다. 메모리랑 디스크 차이였다
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1721309609023/80fc71e9-b17c-4e63-9e83-1f757acb6621.png align="center")
        
    * 실시간 데이터 처리 기술이라서 메모리만 사용할 거라 생각하겠지만 디스크를 활용하여 데이터에 영속성을 부여한다는 것..
        
* 파일 시스템을 사용해도 속도를 향상시킬 수 있는 이유
    
    * 운영체제에서 파일 I/O 성능 향상을 위해 페이지 캐시 영역을 메모리에 따로 생성하여 사용한다
        
    * 페이지 캐시 메모리 영역을 사용하여 한 번 읽은 파일 내용은 메모리에 저장시켰다가 다시 사용하는 방식이기 때문에 카프카가 파일 시스템에 저장하고 전송하더라도 처리량이 높다
        
* 따라서 브로커 장애가 발생하더라도 프로세스를 재시작하면 데이터 손실 없이 다시 처리할 수 있다
    

  
4\. 고가용성

* 3개 이상의 브로커로 운영되는 클러스터는 일부 브로커에 장애가 발생하더라도 무중단으로 안전하고 지속적으로 데이터를 처리할 수 있다
    
    * 프로듀서가 보내는 데이터는 여러 브로커에 저장되기 때문이다
        
* 클레스터로 이루어진 카프카는 데이터의 복제Replication을 통해 고가용성의 특징을 가지게 되었다
    
* 온프레미스(서버를 직접 운영하는 환경)의 서버 랙 또는 퍼블릭 클라우드의 리전 단위 장애에도 데이터를 안전하게 복제할 수 있는 브로커 옵션이 있다
    

  
  
강의 \[https://inf.run/uCwV5\](https://inf.run/uCwV5)