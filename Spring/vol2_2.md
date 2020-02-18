[ToC]

전통적인 SQL과 JDBC API의 조합만으로는 enterprise application에서는 한계가 있다. Spring은 주요 Java data access 기술을 모두 지원. 2장에서는 Spring의 data access 기술에 관한 개념을 정리하고, 기술의 사용 방법을 알아본다.

# data access 기술 개념

## DAO pattern
DAO pattern은 DTO 또는 domain object만을 사용하는 interface를 통해 data access 기술을 외부에 노출하지 않도록 만드는 것.

이를 통해 DAO를 사용하는 코드(service)에 영향을 주지 않고 기술을 변경하거나 여러 기술을 혼합해서 사용할 수 있다. 가장 중요한 장점은 **DAO를 사용하는 service 계층의 코드를 기술이나 환경에 종속되지 않는 순수한 POJO로 개발할 수 있다는 것**.

이를 위해 지켜야 할 사항들은 다음과 같다.
### DAO interface & DI
* DAO는 interface를 이용해 접근하고 DI 되도록 만들어야 함. 
* DAO를 사용하는 service 계층 코드에서 의미 있는 method만 public으로 공개해야 됨. (ex. DAO class에 DI를 위해 넣은 setDataSource() 같은 걸 interface에 추가하지 않도록 주의) 
* 특정 data access 기술에서만 의미 있는 DAO method 이름은 피하도록 함. (ex. persist, merge (x) / add(), update() (o))

### exception handling
* data access 중 발생하는 exception는 대부분 복구할 수 없음. 따라서 DAO 밖으로 던져질 때는 runtime exception이어야 함. DAO method 선언부에 throws SQLException과 같은 내부 기술을 드러내서는 안됨. throws Exception과 같은 무책임한 선언도 마찬가지.
> service 계층 코드는 DAO가 던지는 대부분의 exception은 직접 다뤄야 할 이유가 없음 > runtime exception

* 때로는 DAO가 던지는 exception을 잡아서 business logic(service)에 적용하는 경우가 있다. 중복키 exception이나 optimistic locking 등이 대표적인 예. 그런데 이런 exception에 일관성이 없기 때문에 service 계층에서 기술에 따른 exception을 알고 있어야만 하는 문제가 생김. 이 때문에 Spring은 기술이나 DB의 종류에 상관없이 일관된 의미를 갖는 data exception abstraction을 제공하고, 각 기술과 DB에서 발생하는 exception을 Spring data exception으로 변환해주는 servcie를 제공.

## template & API
data access 기술을 사용하는 코드는 대부분 try/catch/finally와 판에 박힌 반복되는 코드로 작성되기 쉽다. data access 기술은 다양한 exception상황이 발생할 수 있다. 이런 상황에서 server의 제한된 resource에 누수가 발생하지 않도록 exception상황에서도 사용한 resource를 적절히 반환해주는 코드가 반드시 필요함.  
* 그래서 template을 쓰면?  
template은 template/callback pattern을 이용해 이런 판에 박힌 코드를 피할 수 있게 한다. 미리 만들어진 작업 흐름을 담은 template은 반복되는 코드를 제거해주고 exception translation과 transaction synchronization 기능도 함께 제공해준다.
> exception translation이란?  
> 예외를 던질 때, 높은 수준의 abstraction layer의 exception으로 바꿔서 전달.
> (catch 조건문은 낮은 exception. throw는 높은 exception)  
* 단점  
data access 기술의 API를 template이 제공해주는 걸 써야한다는 점이다.

## DataSource
JDBC를 통해 DB를 사용하려면 Connection type의 DB access object가 필요하다. 각 요청마다 Connection을 새롭게 만들고 종료시킨다. 효율적인 성능을 위해 미리 정해진 개수만큼의 DB connection을 pool에 준비해두고, application이 요청할 때마다 pool에서 꺼내 하나씩 할당해주고 다시 돌려받는 식의 pooling 기법을 이용.  
Spring에선 DataSource를 독립된 bean으로 등록하도록 강력히 권장한다. Spring data access 기술의 다양한 service에서 DataSource를 필요로 하고 있기 때문에 공유 가능한 Spring bean으로 등록해줘야 한다.  

Spring에서 사용되는 주요 DataSource의 종류를 알아보자.

### 학습 test & 통합 test를 위한 DataSource
* SimpleDriverDataSource  
Spring이 제공하는 가장 단순한 구현 class. getConnection()호출할 때마다 매번 DB connection을 새로 만들고 pool을 관리하지 않는다. 테스트용.
* SingleConnectionDataSource  
하나의 물리적인 DB connection을 계속 사용. 동시에 두 개 이상의 thread가 동작하는 경우 하나의 connection을 공유하기 때문에 위험. SimpleDriverDataSource에 비해 빠름

### open source | commercial DB connection pool
일반적으로 application level에서 전용 DB pool을 만들어 사용. 대표적으로 다음 두 가지 open source connection pool이 많이 사용됨.
* Apache Commons DBCP
* c3p0 JDBC/DataSource Resource Pool

### JDNI/WAS DB pool
대부분의 Java server는 자체적으로 DB pool service를 제공해줌. DB pool library를 사용해 application level의 전용 pool을 만드는 대신 server가 제공하는 DB pool을 사용해야 하는 경우에는 JNDI를 통해 server의 DataSource에 접근해야 함.
> JNDI는 Java Naming and Directory Interface이다. J2EE의 가장 중요한 스펙 중 하나이다. DB의 환경(사용자 계정, JDBC URL, DB 종류)이 바뀌는 경우 설정 파일만 바꾸면 된다. JNDI 는 프로그램과 데이타베이스간에 타이트하게 묶여있는 커플링을 피하게 해주며, 쉽게 설정,배포하게해준다.  
> WAS server가 부팅 시 JNDI object 등록. 실제 구현기능은 vendor(Tomcat)가 제공함.


# JDBC
JDBC는 low level API이다. JDBC는 표준 interface를 제공하고 각 DB vendor와 개발팀에서 이 interface를 구현한 driver를 제공하는 방식으로 사용된다. 그 덕분에 SQL의 호환성만 유지한다면 JDBC로 개발한 코드는 DB가 변경돼도 그대로 재사용할 수 있는 장점이 있다.  
* entity class와 annotation을 이용하는 최신 ORM도 내부적으로는 DB와의 연동을 위해 JDBC를 이용한다.
* JDBC API를 이용한 개발 방식의 문제점과 한계. 번잡한 코드, DB에 따라 던져지는 체크 예외, SQL은 코드 내에서 직접 문자로, connection과 같은 공유 resource를 release 필요.
* Spring JDBC는 장점을 그대로 유지하면서도 단점을 template/callback pattern을 이용해 극복하게 함.
* 간결한 API, JDBC API에서는 지원되지 않는 편리한 기능

## JDBC 기술과 동작원리
### JDBC 접근 방법
* **SimpleJdbcTemplate**  
JDBC의 모든 기능을 최대한 활용할 수 있는 유연성
* **SimpleJdbcInsert & SimpleJdbcCall**
DB가 제공해주는 메타정보를 활용해서 최소한의 코드만으로 단순한 JDBC 코드를 작성하게 해준다.


### JDBC가 해주는 작업
* Connection 열고 닫기  
Connection과 관련된 모든 작업은 필요한 시점에 알아서 진행해준다. 예외가 발생하면 문제없이 열린 모든 Connection object를 닫아준다. 열고 닫는 시점은 transaction 기능과 맞물려서 결정된다.
* Statement 준비와 닫기
* Statement 실행
* ResultSet loop
* Exception handling and translation
체크 예외인 SQLException을 runtime exception인 DataAccessException 으로 변환해준다.
* transaction handling
Spring JDBC가 이런 대부분의 작업을 해주기 때문에 개발자는 data access logic마다 달라지는 부분만 정의해주면 된다. 개발자가 해야 할 또 한 가지는 connection을 가져올 DataSource를 정의해주는 것이다.

상세한 JDBC object 설명은 생략.

## JDBC DAO
DAO에서 DataSource를 DI받은 뒤 template object(SimpleJdbcTemplate & SimpleJdbcInsert)를 코드로 생성해 인스턴스 변수에 두고 쓴다.

# iBatis SqlMaps
* iBatis는 Java object와 SQL 문 사이의 자동 mapping 기능을 지원하는 ORM framework다. Java obejct만을 이용해 data logic을 작성할 수 있게 해주고, SQL을 별도의 파일로 분리해서 관리하게 해주며, object-SQL 사이의 parameter mapping 작업을 자동으로 해준다.
* 본격적인 ORM인 JPA나 hibernate처럼 새로운 DP programming을 익혀야 하는 부담이 없다. 익숙한 SQL 그대로 이용하면서도 JDBC 코드 작성 불편함을 제거해주고, domain object나 DTO 중심으로 개발이 가능하다는 장점이 있다.
* 가장 큰 특징은 SQL을 Java 코드에서 분리해 별도의 XML 파일 안에 작성하고 관리할 수 있다는 점이다.
* iBatis는 단순한 SQL 분리와 자동 mapping 이상의 많은 기능을 제공해주는 고급 framework다.

## SqlMapClient 생성
iBatis의 핵심 API는 SqlMapClient interface에 담겨 있다. SqlMapClient를 bean으로 등록해두어야 한다. DAO에서 DI 받아 사용해야 하기 때문에 SqlMapClientFactoryBean이 필요하다.
### iBatis 설정파일과 mapping 파일
공통 설정을 담은 XML 파일과 mapping정보를 담은 XML mapping파일 두 가지가 필요하다.  
* **설정파일**  
DataSource, Transaction manager, mapping resource 파일 목록, property, typeAlias, handler, object factory, 설정 property 값  
이 중 DataSource, Tx manager는 bean으로 등록된 것을 사용하는 게 좋다.
* **mapping파일**  
SQL 문, SQL parameter, 실행 결과를 어떻게 Java object로 변환하는지가 담겨 있다. 각 SQL은 고유한 id를 갖고 DAO에서는 이 id를 이용해 SQL을 실행한다.
### SqlMapClient를 위한 SqlMapClientFactoryBean 등록
JDBC의 Connection처럼 모든 data access 작업에서 필요로 하는 object다. SqlMapClient를 singleton bean으로 등록해서 필요한 DAO에서 DI 받아 사용할 수 있다. SqlMapClient는 multi-thread에서 공유해서 사용해도 안전.  
SqlMapClient의 구현 class를 직접 bean으로 등록하는 대신 다음과 같이 SqlMapClientFactoryBean을 이용해 factory bean이 생성해줘야 한다.
```xml
<bean id="sqlMapClient" class="org.springframework.orm.ibatis.SqlMapClientFactoryBean">
    <property name="configLocation" value="classpath:/META-INF/sqlmap/sql-map-config.xml"/>
    <property name="dataSource" ref="dataSource"/>
</bean>
```
## SqlMapClientTemplate
sqlMapClient를 직접 사용하는 대신 Spring이 제공하는 template object인 SqlMapClientTemplate을 이용하는 것이 좋다.  
DAO에서는 SqlMapClient object를 DI 받고 이를 이용해 SqlMapClientTemplate object를 instance 변수에 저장해두고 사용하는 방법을 주로 쓴다.

4.JPA, 5.Hibernate 는 현재 공부하는 application에 해당되지 않는 내용 같아서 생략.

# transactions
EJB(Enterprise JavaBeans)가 제공했던 서비스 중 가장 매력적인 것은 바로 declaritive transaction이다. 경계설정 기능을 이용하면 코드 내에서 직접 transaction을 관리하고 transaction 정보를 parameter로 넘겨서 사용하지 않아도 된다. declarative transaction의 가장 큰 장점은 transaction script 방식의 코드를 탈피할 수 있다는 것.  
transaction script란 하나의 transaction 안에서 동작해야 하는 코드를 한 군데 모아서 만드는 방식이다. 이 방식은 중복이 자주 발생한다. business logic과 data access logic이 한데 코드에 섞여 있는 문제는 말할 것도 없다.  
transaction script 코드에 DAO pattern을 적용해서 data access logic을 분리하더라도, transaction을 명시적으로 시작하고 종료하는 경계 설정코드가 등장하는 것과 매번 transaction 정보를 method parameter로 넘기는 불편은 여전히 남아 있다.  
하지만 declarative transaction 경계설정을 사용하면 이런 문제를 모두 해결할 수 있다.  
EJB의 이런 기능을 복잡한 환경이나 구현조건 없이 평범한 POJO로 만든 코드에 적용하게 해주는 것이 바로 Spring이다.

## Transaction abstraction & synchronization
transaction service는 data access 기술보다 더 다양하다.  
Spring은 data access 기술과 transaction service 사이의 종속성을 제거하고 Spring이 제공하는 transaction abstraction 계층을 이용해 transaction 기능을 활용하도록 만들어준다.

## transaction 경계설정 전략
transaction의 시작과 종료가 되는 경계는 보통 service 계층 object의 method다.  
경계 설정 방법은 코드에 의한 programming과 AOP를 이용한 declarative 방법으로 구분할 수 있다.

## transaction attribute
Spring은 transaction의 경계를 설정할 때 4가지 속성을 지정할 수 있다. 또, declarative transaction에서는 rollback과 commit의 기준을 변경하기 위해 2가지 추가 속성 지정 가능하다.

## data access 기술 transaction의 통합
여러 개의 DB를 독립적으로 사용하지 않는 한 transaction manager는 한 개만 사용.  
하지만 DB는 하나이지만 두 가지 이상의 data access 기술을 하나의 transaction 안에서 사용하는 것도 가능하다.

## JTA를 이용한 global/distributed transaction
한 개 이상의 DB나 JMS의 작업을 하나의 transaction 안에서 동작하게 하려면 server가 제공하는 transaction manager를 JTA(Java Transaction API)를 통해 사용해야 한다. 