# Spring 101

## SOLID - 객체 지향 설계의 5가지 원칙
* SRP 단일 책임 원칙, Single Responsibility Principle

    한 클래스는 하나의 책임만 가져야 한다. 클래스 책임의 범위는 너무 작아도, 너무 커도 안된다. 변경을 할 때 다른 클래스에 미치는 파급력이 적으면 적당한 책임이다.

* OCP 개방-폐쇄 원칙, Open/Closed Principle

    소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야한다. 다형성과 관련이 있는데, 인터페이스(부모)를 제대로 구현하면 클래스들(자식)에서 새로운 기능을 구현할 때 인터페이스 자체를 변경하지 않아도 된다. 

    하지만 `Fruit f = new Apple()` 이 있을 때 다른 클래스로 변경하려면 `Fruit f = new Orange()` 처럼 코드 자체를 변경해야한다. "변경에는 닫혀 있어야"하는 OCP 원칙을 지킬 수 없게 된다. 따라서 객체를 생성하고 관계를 맺는 별도의 설정자가 필요하다.

* LSP 리스코프 치환 원칙, Liskov Substitution Principle

    하위 클래스는 상위 인터페이스 규약을 다 지켜야한다. 정사각형은 직사각형에 포함되므로 직사각형 부모를 둔다고 하자. 이후 height 를 늘리는 함수를 제작한다면 직사각형 객체는 문제가 없지만 정사각형은 width 와 height 이 달라지므로 넓이를 계산할 때 문제가 생긴다. 따라서 정사각형과 직사각형은 치환이 되지 않으므로 상속 관계를 없애야 한다.

* ISP 인터페이스 분리 원칙, Interface Segregation Principle

    클라이언트가 자신이 이용하지 않는 메소드에 의존하지 않아야 한다. 자동차 인터페이스를 운전 인터페이스와 정비 인터페이스로 나눔으로써, 운전자 클라이언트와 정비사 클라이언트로 나뉘어지고 각각 독립적인 메소드를 사용하게 된다.

* DIP 의존관계 역전 원칙, Dependency Inversion Principle

    앞서 본 OCP 예제 `Fruit f = new Apple()` 는 Fruit 이 Apple 에 의존하고 있다. 이처럼 상위 모듈이 하위 모듈에 의존하는 관계를 역전해서, 하위 모듈의 구현으로부터 독립되게 할 수 있다. 즉, 상위 모듈은 하위 모듈에 의존해서는 안되고, 둘 다 추상화에 의존해야한다. 추상화 역시 세부사항에 의존해서는 안된다.

## 객체지향적 코드 작성
서비스를 사용하기 위해 기존에는 `Service Service = new ServiceImpl();` 처럼 직접 인터페이스와 구현체를 가져와야했다. 또한 ServiceImpl 구현체 내에도 `private final Repository repository = new MemoryRepository();` 로 레포지토리 인스턴스와 구현체에 의존하는 형식이었다. 이러한 방법의 문제점은 ServiceImpl 이 사용하는 MemoryRepository 를 변경할 때도 코드를 직접 변경해야하며(OCP 위반), 이는 상위모듈(ServiceImpl)이 하위모듈(Repository)에 의존하는 문제가 있다(DIP 위반).

기존 코드는 역할과 구현이 분리되어있고, 인터페이스를 통해 다형성도 활용하였지만 객체지향 설계 원칙을 준수했다고 보기는 힘들다. `ServiceImpl` 이 인터페이스에만 의존하도록 코드를 변경하면 `private Repository repository;` 가 `public ServiceImpl(Repository repository)`생성자를 통해 구현체를 주입받도록 해야한다. 

이제 main() 에서 실행할 때 AppConfig 객체를 생성하고, `appConfig.memberService()` 처럼 config 클래스에서 구현체를 가져오면 된다. AppConfig 에서는 `ServiceImpl`, `MemoryRepository` 가 주입된 객체를 반환한다. 이제 구현체를 수정하려면 AppConfig 의 내용만 변경하면 된다. 즉, 클라이언트 코드인 ServiceImpl 는 전혀 영향을 받지 않는다.

* 관심사의 분리(Separation of Concerns): 객체를 사용하는 역할과 구성(연결)하는 역할을 분리해야한다. 구현체는 인터페이스로 실행을 하는 데에만 집중한다. 구현체 내에 있는 다른 클래스와의 의존 관계에 대해, 어떤 클래스 구현체가 오더라도 역할을 수행할 수 있어야한다. 인터페이스가 어떤 구현체를 사용할지는 AppConfig(애플리케이션의 전체 동작 방식 구성)를 통해 객체를 생성하고 연결한다.

* 의존관계 주입(Dependency Injection), 제어의 역전(Inversion of Control): ServiceImpl 는 생성자를 통해 어떤 객체가 주입될지는 알 수 없다. 어떤 객체를 사용할지는 외부 AppConfig 가 결정한다. 이는 클라이언트가 스스로 필요한 객체를 생성하는 것이 아니라, 어떤 객체를 사용할지 외부 클래스인 AppConfig 가 제어권을 가져간 셈이다. 이때 AppConfig 를 IoC 컨테이너 또는 DI 컨테이너라고 한다.

* 애자일 소프트웨어 개발: 객체지향 설계는 민첩(Agile)하게 변화에 대응할 수 있게 한다.

## JPA 의 Entity 가 기본 생성자를 가져야하는 이유
JPA는 DB 값을 객체 필드에 주입할 때 기본 생성자로 객체를 생성한 후 이러한 Reflection을 사용하여 값을 매핑하기 때문이다.

지연로딩으로 설정하였기 때문에 임시로 hibernate가 생성한 proxy 객체를 가리키는 것이다. 이러한 proxy 객체는 직접 만든 Department class를 상속하기 때문에 public 혹은 protected 기본 생성자가 필요하게 된다. 
https://hyeonic.tistory.com/191

## @SpringBootApplication
스프링이 정의한 외부 의존성을 갖는 class들을 스캔해서 Bean으로 등록한다. @Component가 선언된 클래스들을 찾아 Bean으로 등록하는 역할도 한다.
https://jungguji.github.io/2020/05/26/SpringBootApplication-%EC%9D%98-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D/

## DTO 의 사용범위
Controller는 View와 도메인 Model의 데이터를 주고 받을 때 별도의 DTO 를 주로 사용합니다. 도메인 객체를 View에 직접 전달할 수 있지만, 민감한 도메인 비즈니스 기능이 노출될 수 있으며 Model과 View 사이에 의존성이 생기기 때문입니다. 

Controller에서 DTO를 완벽하게 Domain 객체로 구성한 뒤 Service에 넘겨주려면, 복잡한 경우 Controller가 여러 Service(혹은 Repository)에 의존하게 됩니다. 이러한 경우 DTO를 Service에게 넘겨주어 Service가 Entity로 변환시키도록 하는 것이 더 나은 방안이라 사료됩니다.
https://tecoble.techcourse.co.kr/post/2021-04-25-dto-layer-scope/

