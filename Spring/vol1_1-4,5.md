# 제어의 역전(IoC)

## Object Factory

### Factory

> UserDaoTest는 UseDao가 담당하던 기능, 어떤 ConnectionMaker 구현 class를 사용할지 결정하는 기능을 떠맡았다.
> 그런데 UserDaoTest는 UserDao의 기능이 잘 동작하는지를 테스트하려고 만든 것.

그래서! UserDao와 ConnectionMaker 구현 class의 object를 만드는 것과 두 개의 object가 연결돼서 사용될 수 있또록 관계를 맺어주는 것을 만들자.

### 설계도로서의 Factory

DaoFactory를 분리했을 때의 장점 중 하나는 application의 component 역할을 하는 obejct(UserDao & ConnectionMAker)와 application의 구조를 결정하는 obejct(DaoFActory)를 분리했다는 것.

> DaoFactory는 application의 obejct들을 구성하고 그 관계를 정의하는 책임을 맡고 있다.


## 제어관계 역전

제어의 역전이라는 건, 간단히 프로그램의 제어 흐름 구조가 뒤바뀌는 것

> 일반적으로 프로그램의 흐름은 main() method와 같이 프로그램이 시작되는 지점에서 다음에 사용할 object를 결정하고, 결정한 object를 생성하고, 만들어진 object에 있는 method를 호출하고, 그 안에서 다음에 사용할 것을 결정하고 호출하는 식의 작업이 반복된다.
> 작업을 사용하는 쪽에서 제어하는 구조이다.

제어의 역전이란 이런 제어 흐름의 개념을 꺼꾸로 뒤집는 것이다. object가 자신이 사용할 object를 스스로 선택하지도, 생성하지도 않는다. 또 자신도 어떻게 만들어지고 어디서 사용되는지를 알 수 없다. 프로그램의 시작을 담당하는 main()과 같은 entry point를 제외하면 모든 object는 이렇게 위임받은 제어 권한을 갖는 특별한 object에 의해 결정되고 만들어진다.

> framework는 제어의 역전 개념이 적용된 대표적인 기술이다. 라이브러리를 사용하는 application code는 흐름을 직접 제어한다. 반면에 framework는 거꾸로 application code가 framework에 의해 사용된다.

# IoC of Spring

> Spring의 핵심을 담당하는 건, Bean Factory or Application Context

## Object Factory를 이용한 IoC

### application context와 설정정보

> Bean: 
> Spring이 IoC 방식으로 관리하는 object. managed object. Spring이 직접 그 생성과 제어를 담당하는 object'만'을 bean이라 함.

> Bean Factory: 
> IoC를 담당하는 핵심 container. bean을 등록, 생성, 조회하고 돌려주고, 관리한다. 보통 이를 확장한 application context를 이용

> application context: 
> Bean Factory를 확장한 IoC container. Bean Factory는 주로 bean의 생성과 제어의 관점. application context는 Spring이 제공하는 부가 서비스를 모두 포함. ApplicationContext extends BeanFactory

application context와 그 설정정보가 설계도 역할. application component 생성, 사용할 관계를 맺어줌.

> @Configuration: BeanFactory를 위한 object 설정을 담당하는 class
> @Bean: object를 만들어주는 method

### step 1: context 생성

new AnnotationConfigApplicationContext(DaoFactory.clss);
와 같이 ApplicationContext type의 object 생성

### step 2: dao 생성

context.getBean("userDao", UserDao.class);
와 같이 UserDao type의 object 생성.
첫 번째 인자는 @Bean을 붙여준 method이름.

## application context의 동작방식

1. application context는 DaoFactory class를 설정정보로 등록해두고 @Bean이 붙은 method 이름을 가져와 Bean list를 만듦
2. client가 application의 getBean()을 호출하면 bean list에서 요청한 이름이 있는지 찾고, 있다면 object를 생성시킨 후 client에 돌려줌.

### application context를 사용하는 이유(DaoFactory와 비교했을 때와의 장점)

1. client는 구체적인 factory class를 알 필요가 없다.
> object factory(DaoFactory와 같은)가 아무리 많아져도 이를 알아야 하거나 직접 사용할 필요가 없다.
> 또, DaoFactory처럼 Java code를 작성하는 대신 XML처럼 단순한 방법을 사용해서 IoC 설정정보를 만들 수 있음.

2. application context는 종합 IoC service 제공
> object 생성과 다른 object와의 관계설정 뿐만 아니라, 만들어지는 방식, 시점과 전략, 자동생성, post processing, 정보의 조합, 설정 방식의 다변화, intercepting 등 다양한 기능 제공

3. bean을 검색하는 다양한 방법 제공
> bean의 이름, type으로 검색 가능하고, 특별한 annotation 설정이되어 있는 bean 검색도 가능

## IoC 용어 정리

> 설정정보/설정 메타 정보^configuration\ metadata^:
> 구성 정보 내지 형상 정보. 또는 application 전체 그림이 그려진 청사진^blueprints^

> (IoC) container:
> IoC 방식으로 bean을 관리한다는 의미에서 application context나 bean factory를 container or IoC container라 함.
> conatiner라는 말 자체가 IoC의 개념을 담고 있음 > application context 대신 Spring container라 하는 사람들도 있음.
> container를 떼고 Spring이라고 부를 때도 있음.