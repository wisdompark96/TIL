# 비즈니스 규칙 엔진
## 목표
테스트 주도 개발 기법으로 새로운 설계 문제를 풀어나가는 방법을 배운다. 유닛 테스트를 구현하는 데 유용한 모킹(mocking)기법도 전반적으로 배운다.

## 비즈니스 규칙 엔진 요구 사항
프로그래머가 아닌 사람도 자신의 워크플로에 비즈니스 로직을 추가하거나 바꿀 수 있는 기능을 만들려 한다. 
비즈니스 규칙 엔진은 간단한 맞춤 언어를 사용해 한 개 이상의 비즈니스 규칙을 실행하는 소프트웨어로 다양한 컴포넌트를 동시에 지원한다.
- 팩트 : 규칙이 확인할 수 있는 정보
- 액션 : 수행하려는 동작
- 조건 : 액션을 언제 발생시킬지 지정
- 규칙 : 실행하려는 비즈니스 규칙을 지정. 보통 팩트, 액션, 조건을 한 그룹으로 묶어 규칙으로 만듦
비즈니스 규칙 엔진의 생산성과 고나련된 좋은 점은 규칙이 기존의 응용프로그램과는 독립된 곳에서 실행, 유지보수, 테스트할 수 있다는 점이다.

## 테스트 주도 개발
사용자가 수행할 기본기능
- 액션 추가
- 액션 실행
- 기본 보고
```
public class BusinessRuleEngine {
    public void addAction(final Action action) {
        throw new UnsupportedOperationException();
    }
    
    public int count() {
        throw new UnsupportedOperationException();
    }
    
    public void run() {
        throw new UnsupportedOperationException();    
    }
}
```

실행할 수 있는 코드로 액션을 만든다. Action 인터페이스를 이용해 비즈니스 규칙 엔진과 구체적 결합을 제거한다.
```
@FunctionalInterface
public interface Action {
    void execute();
}
```
### TDD를 사용하는 이유
- 테스트를 따로 구현하므로 테스트에 대응하는 요구 사항을 한 개씩 구현할 때마다 필요한 요구 사항에 집중하고 개선할 수 있다.
- 코드를 올바르게 조직할 수 있다. 예를 들어 먼저 테스트를 구현하면서 코드에 어떤 공개 인터페이스를 만들어야 하는지 신중히 검토하게 된다.
- TDD 주기에 따라 요구 사항 구현을 반복하면서 종합적인 테스트 스위트를 완성할 수 있으므로 요구 사항을 만족시켰다는 사실을 조금 더 확신할 수 있으며 버그 발생 범위도 줄일 수 있다.
- 테스트를 통과하기 위한 코드를 구현하기 때문에 필요하지 않은 테스트를 구현하는 일(오버엔지니어링)을 줄일 수 있다.

### TDD 주기
1. 실패하는 테스트 구현
2. 모든 테스트 실행
3. 기능이 동작하도록 코드 구현
4. 모든 테스트 실행
실행활에서는 코드를 항상 리팩터링해야 하며 그렇지 않으면 유지보수할 수 없는 코드가 되기 십상이다. 리팩터링하면 코드를 바꿨을 때 뭔가 잘못되어도 의지할 수 있는 테스트 스위트를 갖는다.

### 모킹
1. 목mock 생성
2. 메서드가 호출되었는지 확인
```
@Test
void shouldExecuteOneAction() {
    final BusinessRuleEngine businessRuleEngine = new BusinessRuleEngine();
    final Action mockAction = mock(Action.class);

    businessRuleEngine.addAction(mockAction);
    businessRuleEngine.run();

    verify(mockAction).perform();
}
```
정적 메서드 mock()으로 필요한 목 객체를 만들고 특정 동작이 실행되었는지 확인한다. verify() 메서드로 특정 메서드가 호출되었는지 확인하는 어서션을 만든다.

## 조건 추가하기
### 지역 변수 형식 추론
자바 10은 지역 변수 형식 추론 기능을 지원한다. 형식 추론이란 컴파일러가 정적 형식을 자동으로 추론해 결정하는 기능으로 사용자는 더 이상 명시적으로 형식을 지정할 필요가 없다.
```
//명시적 형식으로 지역변수 선언
Facts env = new Facts();
BusinessRuleEngine businessRuleEngine = new BusinessRuleEngine(env);

//지역 변수 형식 추론
var env = new Facts();
var businessRuleEngine = new BusinessRuleEngine(env);
```

> var 키워드를 사용한 변수는 final이 아니다.
var를 이용한 이득은 주관적이다. 따라서 var를 사용해도 가독성에 문제가 없다면 var를 사용하고 그렇지 않다면 var를 사용하지 않는 것이 좋다.

### switch문
자바 12에서는 새로운 switch문을 이용해 여러 break문을 사용하지 않고도 폴스루를 방지할 수 있다.
```
var forecastedAmount = amount * switch (dealStage) {
    case LEAD -> 0.2;
    case EVALUATING -> 0.5;
    case INTERESTED -> 0.8;
    case CLOSED -> 1;
}
```
새로운 switch를 이용하면 가독성이 좋아질 뿐만 아니라 모든 가능성을 확인하는 소모 검사도 이루어진다. 즉 enum에 switch를 사용하면 자바 컴파일러가 모든 enum 값을 switch에서 소모했는지 확인한다.

### 인터페이스 분리 원칙
인터페이스 분리 원칙(ISP)은 어떤 클래스도 사용하지 않는 메서드에 의존성을 갖지 않아야 한다. 이는 불필요한 결합을 만들기 때문이다.
ISP는 설계가 아닌 사용자 인터페이스에 초점을 둔다. 즉 인터페이스가 커지면 인터페이스 사용자는 결국 사용하지 않는 기능을 갖게 되며 이는 불필요한 결합도를 만든다.
인터페이스 분리 원칙을 따르려면 현재의 개념을 독자적인 작은 개념으로 쪼개야 한다. 이 원칙을 따르면 응집도도 높아진다.

## 플루언트 API 설계
### 플루언트 API란
풀루언트 API란 특정 문제를 더 직관적으로 해결할 수 있도록 특정 도메인에 맞춰진 API를 가리킨다. 플루언트 API의 메서드 체이닝을 이용하면 더 복잡한 연산도 지정할 수 있다.
### 도메인 모델링
- 조건: 어떤 팩트에 적용할 조건(참이나 거짓으로 평가됨).
- 액션: 실행할 연산이나 코드 집합
- 규칙: 조건과 액션을 합친 것. 조건이 참일 때만 액션을 실행한다.

### 빌더 패턴
빌더 패턴은 단순하게 객체를 만드는 방법을 제공한다. 빌더 패턴은 생성자의 파라미터를 분해해서 각각의 파라미터를 받는 여러 메서드로 분리한다.