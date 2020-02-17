[ToC]

전통적인 SQL과 JDBC API의 조합만으로는 enterprise application에서는 한계가 있다. Spring은 주요 Java data access 기술을 모두 지원. 2장에서는 Spring의 data access 기술에 관한 개념으 정리하고, 기술의 사용 방법을 알아본다.

# 개념

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

* 때로는 DAO 던지는 exception을 잡아서 business logic(service)에 적용하는 경우가 있다. 중복키 exception이나 optimistic locking 등이 대표적인 예. 그런데 이런 exception에 일관성이 없기 때문에 service 계층에서 기술에 따른 exception을 알고 있어야만 하는 문제가 생김. 이 때문에 Spring은 기술이나 DB의 종류에 상관없이 일관된 의미를 갖는 data exception abstraction을 제공하고, 각 기술과 DB에서 발생하는 exception을 Spring data exception으로 변환해주는 servcie를 제공.

## template & API
data access 기술을 사용하는 코드는 대부분 try/catch/finally와 판에 박힌 반복되는 코드로 작성되기 쉽다. data access 기술은 다양한 exception상황이 발생할 수 있다. 이런 상황에서 server의 제한된 resource에 누수가 발생하지 않도록 exception상황에서도 사용한 resource를 적절히 반환해주는 코드가 반드시 필요함.  
* 그래서 template을 쓰면?  
template은 template/callback pattern을 이용해 이런 판에 박힌 코드를 피할 수 있게 한다. 미리 만들어진 작업 흐름을 담은 template은 반복되는 코드를 제거해주고 exception translation과 transaction synchronization 기능도 함께 제공해준다.
> exception translation이란?  
> 예외를 던질 때, 높은 수준의 abstraction layer의 exception으로 바꿔서 전달.
> (catch 조건문은 낮은 exception. throw는 높은 exception)  
> 이렇게 함으로써 
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