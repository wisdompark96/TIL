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