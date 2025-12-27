# 정태영 | 백엔드 | 포트폴리오
---
## 기술 스택
- **언어:** Java
- **프레임워크:** SpringBoot, Netty
- **데이터베이스:** MongoDB, Altibase, PostgreSQL, Redis
- **미들웨어:** Kafka, Apache Artemis, Apache Tomcat, Apache Nginx, Apache Httpd
- **데브옵스:** Github, Docker, k8s, Linux

---

### 레거시 시스템 MSA 전환 (2024년)
- https://github.com/teang90/RestructuringMSA

### MSA 환경에서 Redis 이슈(RedisCommandTimeout)와 해결까지
- https://github.com/teang90/ImproveRedisAccess

---

### 주요 프로젝트
### 2024년
### 위치 플랫폼 통합 및 컨테이너 환경의 MSA 전환

- 레거시 VM 기반 위치 플랫폼을 컨테이너 환경의 **MSA 아키텍처로 전환하는 핵심 개발 역할 수행**
- 내부 메시지 처리 구조, 비동기 트랜잭션 처리, 성능 및 동시성 이슈 해결을 주도

#### 아키텍처 설계
- Kafka / REST API / Socket(Netty) 통신을 분리하기 위한 **In/Out Adapter 구조 설계 및 구현**
- 비즈니스 로직을 Core 모듈로 분리하여 외부 통신 변경에 대한 영향 최소화
- 내부 메시지 기반 구조로 어댑터 간 결합도 제거 및 확장성 확보

#### 비동기 트랜잭션 처리
- Kafka, REST API 어댑터에서 **MQ 기반 비동기 내부 메시지 통신 구조 설계 및 구현**
- 요청/응답 트랜잭션 매칭을 위해 **CompletableFuture + Callback 조합 패턴 구현**
- 비동기 환경에서도 동기식 응답 흐름을 유지할 수 있도록 표준 처리 방식 정립
- 해당 구조를 일반화하여 샘플 코드로 정리
  - https://github.com/teang90/bindAsyncTransaction.git

#### Redis 기반 상태 및 트랜잭션 관리
- Core 및 Adapter 모듈 공통으로 사용하는 **Redis 단말 상태 관리 및 트랜잭션 이력 관리 라이브러리 설계·개발**
- 트랜잭션 생성 / 변경 / 삭제 라이프사이클 관리 구조 표준화
- 다수 트랜잭션에서 단일 Key 동시 수정 문제 해결을 위해 **RedisJSON 모듈 적용**

#### 동시성 제어
- 멀티 인스턴스 환경에서 발생한 비즈니스 코어 모듈의 동시성 이슈 분석
- **Redisson 기반 분산 Lock 적용**을 통해 데이터 정합성 및 중복 처리 문제 해결

#### 성능 및 장애 개선
- 트랜잭션 처리 중 반복적인 Redis 접근으로 발생한 **CommandTimeout 이슈 분석 및 개선**
  - CPU 사용률 80 ~ 95% → 30 ~ 40% 감소
  - 일 평균 수십 건 발생하던 Redis 조회 실패 완전 해소
- 부하 테스트 중 Apache Artemis 메시지 처리 지연(약 2초) 현상 분석
  - JMS Connection Pool 문제를 식별하고 수동 설정으로 지연 해소
- Redis 순단 시 Cluster 재연결 불가 문제를 발견
  - **ClusterTopology 설정 추가**로 Redis Cluster 형상 변경 및 장애 상황 대응 가능하도록 개선
