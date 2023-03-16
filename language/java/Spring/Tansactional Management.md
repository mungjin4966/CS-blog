<h1> Spring Transactional Management </h1>

----

<h2> Transactional Management </h2>

- global transcation : 서로 다른 데이터 베이스 간에 트랜잭션이 발생할 수 있는 경우를 분산 트랜잭션이라고 한다. <br>
이를 처리하기 위해서는 트랜잭션 관리자는 어플리케이션 수준이 아닌 서버 수준에 점유를 하고 있어야 하며 JTA(java tansactional API)는 JNDI의 지원과 함께 다른 데이터베이스를 조회하는데 필요하며 트랜잭션 관리자는 분산 트랜잭션의 커밋 또는 롤백 결정하게 된다. <br>
이것은 어플리케이션의 서버 수준의 지식을 필요로 한다. <br>


- Local Transcation : 로컬 트랜잭션은 간단한 JDBC 연결과 같은 단일 RDBMS와 어플리케이션 사이에서 발생된다.

<p>
Global Transaction과 local Transcation 모두 개발자가 스스로 관리를 해야하는 영역으로 만약 JBDC를 사용한다면 트랜잭션 api는 jdbc용을 사용해야 한다. <br>
Hibernate를 사용하는 경우, 어플리케이션 서버의 hibernate 트랜잭션 api와 jta는 Global 트랜잭션을 위한 것이다.

스프링 프레임워크는 다양한 트랜잭션 api에 대한 추상화를 제공하고 있으며 일관된 프로그래밍을 제공하기 위해 모든 문제를 극복하였다. <br>
org.springframework.transaction.PlatformTransactionManager 인터페이스를 이용하여 추상화를 해결하였다. <br>
</p>

----

<h3> @Transcational </h3>

스프링의 트랜잭션은 프로그래밍 방식과 선언 방식 두가지를 제공하고 있다.

- 프로그래밍 방식
  - transaction Template
  - 직접 Platform Transaction Manager 구현

트랜잭션 관리자가 비즈니스 로직과 함께 사용되기 때문에 프로그래밍 방식으로는 널리 사용되지 않는다. <br>
그러나 몇개의 CRUD만 존재하는 어플리케이션에서는 프록시를 통한 부하가 과중한 작업으로 느껴질 수 있어 프로그래밍 방식이 사용될 수 있다. <br>

- 선언적 방식 <br>
  트랜잭션 관리가 비즈니스 로직과 별도로 분리되어 있기 때문에 널리 사용되고 있다. <br>
  스프링 aop 프록시를 활용하여 적절한 트랜잭션 매니져로 호출 주변의 트랜잭션을 구동한다. <br>
  XML과 어노테이션을 활용하여 수행할 수 있다. 
  - @Configuration 어노테이션을 설정을 클래스 상단에 선언하여 @EnableTranscationManagement를 사용한다.
    <pre>
      @Configuration
      @EnableTransactionManagement
      public class SpringConfiguration{
  
      }
    </pre>

  - DataSource와 Transcation Manager를 정의한다.
    <pre>
        @Bean
        public FooRepository fooRepository() {
        // @Transactional 메서드가 있는 클래스를 설정하고 반환합니다.
            return new JdbcFooRepository(dataSource());
        }
        
        @Bean
        public DataSource dataSource() {
        // 필요한 JDBC DataSource 설정 및 반환
        }
    </pre>
    
    메소드 혹은 구현체 클래스를 상단에 @Transactional 어노테이션을 사용한다. <br>
    클래스 수준에서 사용되는 경우 메소드 전체에 전역으로 사용된다. <br>
    <br>
  - @Transaction 어노테이션의 호출원리
     <pre>
        public class SampleService{
            @Transactional
            public void serviceMethod(){
                // Call to dao layer
            }
        }
    </pre>
  - SampleService가 클래스에 주입되면 Spring은 내부적으로 아래와 같은 클래스를 생성하여 대신 주입한다.
    <pre>
        class ProxySampleService extends SampleService {
            private SampleService sampleservice;
    
            public ProxySampleService(sampleservice s){
                this.sampleservice = s;
            }
    
            @Override
            public void sampleMethod(){
                try{
                    sampleservice.sampelMethod();
                    // 트랜잭션 커밋
                }catch(Exption e){
                    // transaction롤백
                }
            }
        }
    </pre>
    위와 같은 방식을 proxy 패턴이라고 하며 실제 호출되는 메소드는 내부적으로 target(본체)를 호출한다.
    <br>
  <br>
- @Transactional 어노테이션 속성 
  - propagation
    - 트랜잭션 전파를 위한 설정으로 이것은 트랜잭션 동작을 설정하는데 매우 중요한 속성이다. 
      - REQUIRED (default) - 현재 트랜잭션 지원, 존재하지 않는 경우 새로운 트랜잭션을 생성
      - REQUIRES_NEW - 새로운 트랜잭션을 생성하고 존재하지 않는 경우 현재 트랜잭션을 일시적으로 중단한다.
      - MANDATORY - 현재 트랜잭션을 지원하고 존재하지 않는 경우 예외를 던진다.
      - NESTED - 현재 트랜잭션이 있는 경우 중첩된 트랜잭션 내에서 실행한다.
      - SUPPORTS - 현재 트랜잭션을 지원하지만 존재하지 않는 경우 비트트랜잭션으로 실행
  - isolation 트랜잭션 격리의 수준, 다른 트랜잭션과 격리되어야 하는 수준을 결정한다.
    - DEFAULT - 데이터 소스의 기본 격리 수준
    - READ_UNCOMMITTED - Dirty Read, Non-Repeatable Read 및 Phontom Read가 발생될 수 있음을 나타낸다. 다른 트랜잭션의 커밋되지 않은 데이터도 읽을 수 있다.
    - READ_COMMITTED - Dirty Read를 방지하고 번복할 수 없으며 Phontom Read가 발생할 수 있음을 나타낸다. 커밋된 데이터만 읽음 반복조회시 커밋 시점에 따라 데이터 상이
    - REPEATABLE_READ - Dirty Read와 Non-Repeatable Read가 방지되지만 Phantom Read가 발생할 수 있음을 나타낸다. 반복적으로 조회하여도 동일한 데이터를 보장한다.
    - SERIALIZABLE - Dirty Read와 Non-Repeatable Read, Phantom Read가 방지될 수 있음을 나타내며 데이터 처리의 직렬화를 보장한다.
    - readOnly - 트랜잭션이 읽기 전용인지 또는 읽기/쓰기인지 여부
    - TimeOut - 트랜잭션 타임아웃(처리 시간초과)
    - rollbackFor - 트랜잭션의 롤백을 발생시켜야 하는 예외(Exception) 클래스의 배열
    - rollbackForClassName - 트랜잭션의 롤백을 발생시켜야 하는 예외 클래스 이름의 배열
    - noRollbackFor - 트랜잭션 롤백을 유발하지 않아야 하는 예외 클래스 객체의 배열
    - noRollbackForClassName - 트랜잭션 롤백을 유발하지 않아야 하는 예외 클래스 이름의 배열

 
 
