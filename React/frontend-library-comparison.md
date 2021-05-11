# 시작하기 앞서
front-end는 유저와 상호작용하는 모든 파트. 눈에 보여지는 부분 + 웹 브라우저에서 구동되는 로직.

back-end는 보여지는 부분 뒤에 있는 영역. 비즈니스 로직을 처리하는 기능을 담당.

JavaScript framework(or library)는 SPA라 불리는 요즈음의 어플리케이션을 개발하는데 도움을 준다.
React, Vue, Angular가 대표적.

위와 같은 기술은 사용자가 지저분한 일을 하지 않고도 강력한 web application을 개발할 수 있게 도구들과 code를 제공. 또한, 개발 시간을 줄여주고, 골치아픈 일을 줄여줌.

# Histories of Vue, React, and Angular
* React: Facebook의 엔지니어가 개발. PHP용 HTML 컴포넌트 framework인 XHP에 영향 받음.<sup>[1](#footnote_1)</sup> 2011년 Facebook의 뉴스피드에 처음 적용되었다가 2012년 Instagram.com에 적용. 2013년 오픈 소스화. Facebook, Twitter, Whatsapp, Instagram, Microsoft, Slack, Asana, Airbnb 등에서 사용.

* Angular: 2010년 Google에 의해 개발됨. TypeScript-based. 2016년 Angular2 released. The Guardian, Weather.com 등에서 사용.

* Vue: 셋 중 가장 어리다. 2014년 전 구글 개발자 Evan You에 의해 개발됨. 지난 몇 년 간 빠르게 인기를 얻고 있음. React만큼 유명하지 않지만, Alibaba, Gitlab 등에서 사용.

# Overview of advantages and limitations
## React
* **Advantages**
가볍다. HTML과 JavaScript를 결합한 간단한 문법 구조를 제공하여 배우기 쉽다.
빠르다. Virtual DOM 구현과 rendering 최적화 때문에
최고의 Progressive Web App (PWA<sup>[2](#footnote_2)</sup>) 지원. 특히 create-react-app template generator와 함께
components로 함수형 프로그래밍을 구현. 이로써 app을 유지보수하고 build하기 쉬워짐.
* **Limitations**
개발자가 직접 design을 정해야 함.
지속적으로 업데이트되어 최신을 유지하기 어려울 수 있음
JSX를 사용하여 학습 장벽이 될 수 있음

## Vue
* **Advantages**
디테일한 문서를 제공하여 러닝 커브가 낮음. HTML와 JavaScript만 알면 강력한 SPA를 개발할 수 있음
부정적 영향 없이 기존 인프라에 쉽게 통합할 수 있음.
virtual DOM의 이점을 가짐(Angular보다 빠름). 작고 빨라 다른 framework보다 나은 성능을 제공
* **Limitations**
참조 자료가 많이 없음.

## Angular
* **Advantages**
TypeScript 사용. type checking과 외부 도구 지원이 뛰어남
Google의 지원. 디테일한 문서와 큰 커뮤니티.
Angular-language-service가 외부 HTML 템플릿 파일 내부에서 자동 완성을 허용하여 개발 속도를 높일 수 있음.
* **Limitations**
다양한 structures 제공 like Injectables, Components, Pipes, Modules, and more. Vue와 React가 component에 집중하는 반면, 이것들이 Angular를 배우기 어렵게 함.
real DOM으로 동작하기에 느린 성능. (ChangeDetectionStrategy를 사용하여 렌더링 프로세스를 수동으로 할 수 있지만)

# Framework philosophies
## React
요소를 DOM에 렌더링하고 효과적으로 컨트롤하는 가벼운 library
React의 핵심 기능은 components와 sub-components를 빌드하는 것(저들은 web app의 UI 조각들)
브라우저를 새로고침하지 않고 렌더링하는 SPA를 구축하기 위한 동적 routing library(like React Router)를 제공
## Vue
React와 Angular의 중간 영역 같음. React보다 많고, Angular보다 적은 tool. built-in state 관리와 router 제공
그러나 HTTP 클라이언트 기능이나 양식 유효성 검사 기능은 없음. Vue는 더 빠르고 버그없는 성능을 위해 가상 DOM을 사용.
Vue는 UI를 build하고 재사용가능한 component를 만드는데 중점을 둠.
## Angular
세 가지 프레임워크 중 가장 많은 기능을 제공. 사용자 인터페이스 제어, 사용자 입력 처리, 양식 유효성 검사, 라우팅, 상태 관리, 테스트, PWA 기능 등
React와 달리 많은 도구들을 제공.
종속성을 추가하고 배포하기 위해 Angular 프로젝트를보다 쉽게 관리 할 수있는 공식 CLI

# Popularity
사용할 framework 고려할 때, 인기는 몇 가지 이유로 중요.
* 직업 찾기가 쉬워짐
* 사라지지 않음
* 커뮤니티가 크게 형성되므로 3rd 파티 라이브러리나 도구들이 많음
* 많은 참고자료, 문서, 튜토리얼 등으로부터 쉽게 도움 받을 수 있음

## Google Trends
* 실제 현장에서 사용하는 것과 같진 않지만 어떤 프레임워크가 뜨고 있는지, 사람들이 무엇에 관심을 갖는지 알 수 있음.
![](https://i.imgur.com/piaezOv.png)

## NPM Downloads
![](https://i.imgur.com/UJSI517.png)

## Most Wanted Web Frameworks
![](https://i.imgur.com/rIoifAy.png)

# Syntax
## React
JSX : JavaScript + HTML
프로젝트를 빌드하면 JSX는 일반 JavaScript로 컴파일됨.
```
import React from "react"

function HelloWorld(props) {
  return <li>Hello World</li>
}

export default HelloWorld
```
JavaScript 코드로 component를 생성.

## Vue
React와 Angular와 다르게 일반 JavaScript 사용.
JSX나 TypeScript를 배울 필요 없음.
```
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  }
})
```

## Angular
TypeScript : superset to javaScript
TypeScript를 컴파일하면 브라우저에 호환되는 JavaScript가 됨.

```
import { Component } from '@angular/core';

@Component ({
   selector: 'my-app',
   template: `<h1>Hello World</h1>`,
})
export class AppComponent{}
```
# 가상DOM
* DOM: Document Object Model
    * 문서 객체: html 태그들을 JavaScript가 이용할 수 있는 객체
    * 브라우저가 JavaScript가 이용할 수 있게끔 트리구조로 만든 객체 모델
    * JavaScript는 이를 조작할 수 있는 문법이고 언어
## 가상DOM이 나오게 된 이유
* 메모리 누수와 속도
만일 개발자가 h1 태그를 찾는 코드를 변수에 저장하지 않고, 매번 h1에 접근한다면? 매 단계마다 document 객체를 훑으며 찾고 이는 곧 메모리 누수로 이어진다.
네이게이션을 열었을 때 특정 영역이 빨갛게 변하면서 위치가 바뀌는 경우 > 2번 이상의 (작은 규모)레이아웃 일어남
* 코드량
id의 고유성을 지키기 위해 태그의 네이밍을 정할 때 심사숙고.
해당 태그에 접근하기 위해 작성하는 메서드가 짧지 않음
## 가상DOM 이란
실제DOM의 가벼운 사본
1. 변화가 일어났다. 변화된 버전을 가상DOM으로 바꿈
2. 가상DOM의 현재와 가거 비교
3. 바뀐 부분만 적용

# SPA
![](https://i.imgur.com/JOWdOah.png)
## 장점
1. 클라이언트가 모든 페이지를 가지고 있으므로 앱과 같은 자연스러운 사용자 경험을 제공합니다. 사실상 로딩 이후에는 모바일 앱과 동일한 방식으로 동작을 한다고 볼 수 있습니다.
2. 페이지를 이동 하더라도 필요한 부분 (컴포넌트)만 부분적으로 교체하면 되므로 효율성이 증가합니다.
3. 서버가 해야 할 화면 구성을 클라이언트가 수행하므로 서버 부담이 경감됩니다.
4. 모듈화 또는 컴포넌트별 개발이 용이합니다.
5. 백엔드와 프론트엔드가 비교적 명확하게 구분됩니다.
6. 앱과 웹이 동일한 서버를 이용할 수 있습니다.
7. PWA(Progressive Web App)과 궁합이 잘 맞습니다.
## 단점
1. 처음 접속시에 사이트 구성과 관련된 모든 리소스를 한 번에 받기 때문에 초기 구동 속도가 느릴 수 있습니다.
2. 데이터를 별도로 요청하고 받아와서 화면을 구성하므로 설계 방식에 따라서는 화면이 변하는 모습이 사용자에게 노출될 수 있습니다. 별로 보기 좋지는 않습니다.
3. SPA 구조 상 데이터 처리는 클라이언트에서 하는 경우가 많은데, 중요한 비즈니스 로직이 존재한다면 사용자가 굳이 파헤쳐 보겠다 하면 막기 어렵습니다.
4. 검색엔진 최적화(SEO)가 어렵습니다.

# vs. JSP
* JSP = Java + HTML + JavaScript
* JSP(Java Server Pages)는 서버 사이드 스크립트 언어.
* JSP는 서버 측에서 완성된 리소스를 클라이언트로 전송
* React를 사용한 앱에서는 클라이언트에서 화면을 그림
    * 최소한의 리소스와 렌더링할 JavaScript를 클라이언트에 전송
    * 브라우저에서 클라이언트 컴퓨팅 리소스를 사용하여 그림
    * 필요한 정보는 서버로 요청하여 받음.
* React가 도입된 이유는 동적 화면에 대한 요구가 많아
    * 서버에서 렌더링하는 것이 비효율적. 반응이 느리고 사용성이 저하.
    * 서버 부하가 적어지는 건 부수효과
* ajax와 DOM조작이 있으면 SPA와 같은 효과는 낼 수 있음
    * JavaScript 언어 특성과 기존 방법 상 JavaScript 영역이 늘어나면 관리가 쉽지 않았으나 React 등장으로 JavaScript로 충분히 거대한 코드를 작성하기 쉽게 바뀜


# 각주
<a name="footnote_1">1</a>:  [“React (JS Library): How was the idea to develop React conceived and how many people worked on developing it and implementing it at Facebook?”. 《Quora》.](https://www.quora.com/React-JS-Library/How-was-the-idea-to-develop-React-conceived-and-how-many-people-worked-on-developing-it-and-implementing-it-at-Facebook/answer/Bill-Fisher-17)

<a name="footnote_2">2</a>: PWA는 최고의 웹과 최고의 앱을 결합한 경험이다. 브라우저를 통해 처음 방문한 사용자에게 유용하며, 설치가 필요하지 않다. 사용자가 PWA와 관계를 점진적으로 형성할수록 성능이 더욱 강력해 질 것이다. 느린 네트워크에서도 빠르게 로드되고, 관련된 푸시 알림을 전송한다. 모바일 앱처럼 전체 화면으로 로드되고, 홈 화면에 아이콘이 있다.

# 참고자료
https://www.educative.io/blog/react-angular-vue-comparison
https://velog.io/@mollog/React%EC%97%90%EC%84%9C%EC%9D%98-%EA%B0%80%EC%83%81%EB%8F%94-%EA%B0%9C%EB%85%90
https://paperblock.tistory.com/87
https://okky.kr/article/678071