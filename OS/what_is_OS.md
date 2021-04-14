# what is O/S
- 컴퓨터의 성능을 높이고(performance), 사용자에게 편의성 제공(Convenience)을 목적으로 하는 컴퓨터 하드웨어 관리하는 프로그램
- 사용자와 하드웨어 간(혹은 응용 프로그램과 시스템 자원 간) 인터페이스
- 종류 : Windows, Linux, Mac OSX, iOS
- 하는 일 : processor, memory, disk 등 하드웨어 자원을 효율적으로 관리 / 보안, 네트워크 관리

# 목적
- 주된 목적은 하드웨어 관리
- 사용자에게 편의 제공

# 부팅 
- 부팅 시 CPU에서 ROM에 있는 내용을 읽는다.
- ROM에 있는 POST가 컴퓨터의 상태를 검사하고,  boot loader가 디스크에 있는 O/S를 RAM에 가져온다.
- boot loading 과정이 부팅
- POST : Power-On Self-Test
- 운영체제는 컴퓨터의 전원이 꺼질 때 종료한다.

# 구성
![](https://i.imgur.com/9HyimAf.png)
- kernel과 shell(command interpreter)로 나뉜다.
    - kernel은 O/S가 수행하는 모든 것이 저장되어 있음.
    - shell은 사용자가 요청하는 명령어를 해석하여 kernel에 요청하고 그 결과를 출력
- application은 O/S 위에서 동작한다. (운영체제가 제공하는 자원만 사용 가능)

# O/S의 위치
- application은 특정 O/S에 맞춰서 만드므로, 한 application은 다른 O/S에서 수행할 수 없음.
- application은 H/W 자원을 직접 사용하지 않고, O/S가 제공하는 자원만 사용 가능

[출처] https://velog.io/@codemcd/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9COS-1.-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C%EB%9E%80