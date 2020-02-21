# JDBC strategy pattern의 최적화
지금까지 해온 작업. 즉, 변화하는 코드인 PreparedStatement를 strategy로. 변화하지 않는 코드를 context(전체적인 흐름을 담은)로. client는 strategy object를 정하고, 이를 context의 parameter로 넘겨주어 실행하는 작업.  
여기서 두 가지 불만이 있다. 첫째, DAO method마다 새로운 strategy 구현 class를 만들어야 하는 점. 둘째, DAO method에서 strategy에 전달할 User와 같은 정보가 있는 경우, 이를 위해 contructor와 전달 받은 object를 저장해둘 instance variable을 번거롭게 만들어야 한다는 점.  
이 두가지 문제를 해결할 수 있는 방법은?
## local class
strategy class를 매번 독립된 파일로 만들지 말고 Dao class 안에 inner class(method 안에 정의되는)로 정의해버리는 것.
> nested class는 static class(독립적으로 object로 만들어질 수 있는)와 inner class(자신이 정의된 class의 object 안에서만 만들어질 수 있는)로 구분.

이 방법으로 class 파일이 하나 줄었고, method 안에서 strategy 구현체 생성 logic을 함께 볼 수 있어 코드를 이해하기도 좋다.  
또 한가지 장점은 local class에서 자신이 선언된 곳의 정보에 접근할 수 있음.