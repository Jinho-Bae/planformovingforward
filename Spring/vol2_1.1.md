# IoC container
    
object의 생성과 관계설정, 사용, 제거 등의 작업을 application code 대신 IoC container가 담당.
Spring의 IoC container = applicationContext

## IoC container 이용하기
```java
StaticApplicationContext ac = new StaticApplicationContext(); // 빈 container

```
빈 container를 만들었다. IoC container로서 동작하려면 POJO class와 설정 메타정보가 필요함.

### POJO class
application의 핵심 code를 담고 있는 POJO class. 각각의 POJO는 특정 기술과 스펙에서 독립적일뿐더러 의존관계에 있는 다른 POJO와 느슨한 결합을 갖도록 만들어야 함.

### 설정 메타정보
Spring의 설정 메타정보는 BeanDefinition interface로 표현되는 순수한 추상 정보. 이 interface로 만들어진 object를 활용해 IoC와 DI 작업 수행.

> Spring의 메타정보는 특정한 file format이나 형식에 제한되거나 종속되지 않는다. XML, @, property, Java code 어떤 것이든 상관없다.

Spring IoC container는 각 bean에 대한 정보를 담은 설정 메타정보를 읽어들인 뒤, 이를 참고해서 bean object를 생성하고 property나 contructor를 통해 의존 object를 주입해주는 DI 작업을 수행.

> Spring application: 
> POJO class와 설정 메타정보를 이용해 IoC container가 만들어주는 object의 조합

## IoC container 종류
* StaticApplicationContext

code를 통해 bean 메타정보를 등록하기 위해 사용. test용
* GenericApplicationContext

Static과는 달리 XML file과 같은 걸 reader를 통해 읽어들여 메타정보로 전환해서 사용. 대부분 직접 이용할 일은 없지만, JUnit test가 자동으로 만드는 게 이것.
* GenericXmlApplicationContext

XmlBeanDefinitionReader를 내장
* WebApplicationContext

가장 많이 사용. IoC container의 역할은 초기에 bean object를 생성하고 DI 한 후 최초로 application을 기동할 bean 하나 제공해주는 것까지. 웹 환경에서는 main() 대신 servlet container가 HTTP 요청에 mapping되어 있는 servlet을 실행해줌. 요청이 servlet으로 들어올 때마다 getBean()으로 필요한 bean을 가져와 정해진 method를 실행. 또 다른 특징은 자신이 만들어지고 동작하는 환경인 web module에 대한 정보에 접근할 수 있다는 점. 이를 이용해 web으로부터 정보를 가져오거나, web에 container 자신을 노출할 수 있음.

## IoC container hierarchical structure

### 부모 context를 이용
DI를 위해 bean을 찾을 때 root applicationContext까지 검색. 검색 순서는 자신이 먼저이기 때문에 부모 context가 같은 이름의 bean을 갖고 있어도 그건 무시됨. 따라서 기존 설정을 수정하지 않고 하위 context를 추가하는 것으로 bean 구성을 바꿀 수 있음.

계층 구조를 이용하는 또 한가지 이유는 여러 applicationContext가 공유하는 설정을 만들기 위해서.

### context 계층구조
* child는 parent에 의존적.
    * child를 통해 정의된 bean은 parent를 통해 정의된 같은 종류의 bean 설정을 override
    * 따라서 child가 우선함.

## Web application의 IoC container 구성
Spring IoC container를 사용하는 방법은 크게 세 가지. 두 가지 방법은 web module 안에 container를 두는 것, 나머지 하나는 enterprise level에 두는 법. web application 안에 두는 방법을 알아보자.

Java server에는 하나 이상의 web module을 배치할 수 있음. Spring을 사용한다면 보통 web module^WAR^ 형태로 application을 배포함. Java server 기술이 막 등장했던 초기에는 URL당 하나의 servlet을 만듦. 하지만 최근에는 많은 web 요청을 한 번에 받을 수 있는 대표 servlet을 등록해두고, 공통적인 선행 작업을 수행한 후, 각 요청의 기능을 담당하는 handler class 호출하는 게 일반적. 이렇게 몇 개의 servlet이 중앙집중식으로 모든 요청을 다 받아서 처리하는 이런 방식을 **front controller pattern**이라고 한다. 따라서 Spring web application에 사용되는 servlet은 많아야 두셋.

web application 안에서 동작하는 IoC container는 servlet안에서 or web application level에서 만들어짐. 보통은 두 가지 방식을 모두 사용. Servlet이 하나 이상 등록된다면 container(applicationContext) 개수도 늘어남.

### web application의 context hierarchical structure
* root application context는 web application 안에.
* 각 servlet 안에 하위 context가 존재.

일반적으로 front controller servlet은 하나만 만든다. 하지만 여러 개의 자식 context가 없다면 왜 이렇게 계층 구조로 만들까? 이유는 웹 기술에 의존적인 부분과 그렇지 않은 부분을 구분하기 위해서다.

***
p.77~82 구현 내용은 pass