## 도메인
개발자 입장에서 온라인 서점은 구현해야 할 소프트웨어의 대상이 된다. 온라인 서점 소프트웨어는 온라인으로 책을 판매하는 데 필요한 상품조회, 구매, 결제, 배송 추적 등의 기능을 제공해야한다. 이떼, '온라인 서점' 은 소프트웨어로 해결하고자 하는 문제 영역, 즉 도메인(domain)에 해당한다.
  
한 하위 도메인은 다른 하위 도메인 도메인과 연동하여 완전한 기능을 제공한다. 예를 들어, 고객이 물건을 구매하면 주문, 결제, 배송, 혜택 하위 도메인의 기능이 엮이게 된다.
특정 도메인을 위한 소프트웨어라고 해서 도메인이 제공해야 할 모든 기능을 구현하는 것은 아니다. 많은 온라인 쇼핑몰이 자체적으로 배송시스템을 구축하기보다는 외부 배송 업체의 시스템을 사용하고 일부 필요한 기능만 연동한다.  
도메인마다 고정된 하위 도메인이 존재하는 것은 아니다.  
하위 도메인을 어떻게 구성할지 여부는 상황에 따라 달라진다. 기업 고객을 대상으로 대형 장비를 판매하는 곳은 온라인으로 카탈로그를 제공하고 주문서를 받는 정도만 필요할 것이다. 온라인 결제나 배송추적과 같은 기능을 제공할 필요가 없다.  

## 도메인 모델
도메인 모델에는 다양한 정의가 존재하는데 기본적으로 도메인 모델은 특정 도메인을 개념적으로 표현한 것이다.  
즉, 도메인 모델을 사용하면 여러 관계자들이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는 데 도움이 된다.   
도메인을 이해하려면 도메인이 제공하는 기능과 도메인의 주요 데이터 구성을 파악해야 하는데, 이런 면에서 기능과 데이터를 함께 보여주는 객체 모델은 도메인을 모델링하기에 적합하다.  
도메인 모델을 객체로만 모델링할 수 있는것은 아니다.  
도메인 모델을 표현할 때 클래스 다이어그램이나 상태 다이어그램과 같은 UML 표기법만 사용해야 하는 것은 아니다. 관계가 중요한 도메인이라면 그래프를 이용해서 도메인을 모델링할 수 있다. 도메인을 이해하는데 도움이 된다면 표현 방식이 무엇인지는 중요하지 않다.  
도메인 모델은 기본적으로 도메인 자체를 이해하기 위한 개념 모델이다. 개념 모델을 이용해서 바로 코드를 작성할 수 있는 것은 아니기에 구현 기술에 맞는 구현 모델이 따로 필요하다. 개념 모델과 구현 모델은 서로 다른것이지만 구현 모델이 개념 모델을 최대한 따르도록 할 수는 있다.  
## 도메인 모델 패턴
일반적인 애플리케이션의 아키텍처는 네 개의 계층으로 구성된다.
- 사용자인터페이스(UI) 또는 표현(Presentation) : 사용자의 요청을 처리하고 사용자에게 정보를 보여준다. 여기서 사용자는 소프트웨어를 사용하는 사람뿐만 아니라 외부 시스템도 사용자가 될 수 있다.
- 응용(Application) : 사용자가 요청한 기능을 실행한다. 업무 로직을 직접 구현하지 않으며 도메인 계층을 조합해서 기능을 실행한다.
- 도메인 : 시스템이 제공할 도메인의 규칙을 구현한다.
- 인프라스트럭처(Infrastructure) : 데이터베이스나 메시징 시스템과 같은 외부 시스템과의 연동을 처리한다.

도메인 모델은 아키텍처상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴을 말한다.  
도메인 계층은 도메인의 핵심 규칙을 구현한다. 주문 도메인의 경우 '출고 전에 배송지를 변경할 수 있다' 는 규칙과 '주문 취소는 배송 전에만 할 수 있다' 는 규칙을 구현한 코드가 도메인 계층에 위치하게 된다. 이런 도메인 규칙을 객체 지향 기법으로 구현하는 패턴이 도메인 모델 패턴이다.

> 개념 모델은 순수하게 문제를 분석한 결과물이다. 개념 모델은 데이터베이스, 트랜잭션 처리, 성능, 구현 기술과 같은 것들을 고려하고 있지 않기 때문에 실제 코드를 작성할 때 개념 모델을 있는 그대로 사용할 수 없다. 그래서 개념 모델을 구현 가능한 형태의 모델로 전환하는 과정을 거치게 된다.

핵심 규칙을 구현한 코드는 도메인 모델에만 위치하기 때문에 규칙이 바뀌거나 규칙을 확장해야 할 때 다른 코드에 영향을 덜 주고 변경 내역을 모델에 반영할 수 있게 된다.

## 도메인 모델 도출
구현을 시작하려면 도메인에 대한 초기 모델이 필요하다. 도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것이다.
이 과정은 요구사항에서 출발한다.

> 문서화를 하는 주된 이유는 지식을 공유하기 위함이다. 실제 구현은 코드에 있으므로 모든것은 코드를 보면 알 수 있지만 코드는 상세한 모든 내용을 다루고 있기 때문에 코드를 이용해서 전체 소프트웨어를 분석하려면 많은 시간을 투자해야한다. 


## 엔티티와 밸류
도출한 모델은 크게 엔티티(Entity)와 밸류(Value)로 구분할 수 있다.
### 엔티티
엔티티의 가장 큰 특징은 식별자를 갖는다는 것이다. 식별자는 엔티티 객체마다 고유해서 각 엔티티는 서로 다른 식별자를 갖는다.  
주문에서 배송지 주소가 바뀌거나 상태가 바뀌더라도 주문번호가 바뀌지 않는 것처럼 엔티티의 식별자는 바뀌지 않는다. 엔티티를 생성하고 엔티티의 속성을 바꾸고 엔티티를 삭제할 때까지 식별자는 유지된다.


엔티티의 식별자는 바뀌지 않고 고유하기 때문에 두 엔티티 객체의 식별자가 같으면 두 엔티티는 같다고 판단할 수 있다.

### 엔티티의 식별자 생성
엔티티의 식별자를 생성하는 시점은 도메인의 특징과 사용하는 기술에 따라 달라진다. 흔히 식별자는 다음 중 한 가지 방식으로 생성한다.
- 특정 규칙에 따라 생성
- UUID 사용
- 값을 직접 입력
- 일련번호 사용(시퀀스나 DB의 자동 증가 칼럼 사용)

### 밸류 타입
ShippingInfo 클래스는 받는 사람과 주소에 대한 데이터를 갖고 있다.
```
public class ShippingInfo {
    private String receiverName;
    private String receiverPhoneNumber; //받는 사람

    private String shippingAddress1;
    private String shippingAddress2;
    private String shippingAddress3; //주소
}
```
receiverName 필드와 receiverPhonenumber필드는 서로 다른 두 데이터를 담고 있지만 두필드는 개념적으로 받는 사람을 의미한다.  
밸류 타입은 개념적으로 완전한 하나를 표현할 때 사용한다. 
```
public class Receiver {
    private String name;
    private String phoneNumber;
    ...
}
```

데이터 변경 기능을 제공하지 않는 타입을 불변(immutable)이라고 표현한다. 밸류 타입을 불변으로 구현하는 이유는 여러 가지가 있는데 가장 중요한 이유는 불변타입을 사용하면 보다 안전한 코드를 작성할 수 있다느 것이다.

### 엔티티 식별자와 밸류 타입
엔티티 식별자의 실제 데이터는 String과 같은 문자열로 구성된 경우가 많다. 이런 식별자는 단순한 문자열이 아니라 도메인에서 특별한 의미를 지니는 경우가 많기 때문에 식별자를 위한 밸류타입을 사용해서 의미가 잘 드러나도록 할 수 있다.

### 도메인 모델에 set 메서드 넣지 않기
도메인 모델에 get/set 메서드를 무조건 추가하는 것은 좋지 않은 버릇이다. 특히 set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 한다.
또 다른 문제는 도메인 객체를 생성할 때 완전한 상태가 아닐 수도 있다는 것이다.
불변 밸류 타입을 사용하면 자연스럽게 밸류 타입에는 set메서드를 구현하지 않는 다. set 메서드를 구현해야할 특별한 이유가 없다면 불변 타입의 장점을 살릴 수 있도록 밸류 타입은 불변으로 구현한다.