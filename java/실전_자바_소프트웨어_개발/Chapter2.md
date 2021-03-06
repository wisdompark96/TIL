# 입출금 내역 분석기
## 요구사항
- 은행 입출금 내역의 총 수입과 총 지출은 각각 얼마인가? 결과가 양수인가? 음수인가?
- 특정달엔 몇 건의 입출금 내역이 발생했는가?
- 지출이 가장 높은 상위 10건은 무엇인가?
- 돈을 가장 많이 소비하는 항목은 무엇인가?

## KISS 원칙
KISS(Keep it short and simple)원칙을 이용해 응용프로그램 코드를 한 개의 클래스로 구현한다.

```
public class BankTransactionAnalyzerSimple {
    private static final String RESOURCES = "src/main/resources/";

    public static void main(String... args) throws IOException {
        final Path path = Paths.get(RESOURCES + args[0]);
        final List<String> lines = Files.readAllLines(path);
        double total = 0d;
        for(final String line: lines) {
            final String[] columns = line.split(",");
            final double amount = Double.parseDouble(columns[1]);
            total += amount;
        }
    }
}
```
발행할 만한 문제를 어떻게 처리할지 고려하는 것이 좋다.
- 파일이 비어있다면?
- 데이터에 문제가 있어서 금액을 파싱하지 못 한다면?
- 행의 데이터가 완벽하지 않다면?

특정달에 몇건의 입출금 내역이 발생했는지를 처리하기 위해 주어진 월을 선택하도록 로직을 바꾼다.

```
final Path path = Paths.get(RESOURCES + args[0]);
final List<String> lines = Files.readAllLines(path);
double total = 0d;
final DateTimeFormatter DATE_PATTERN = DateTimeFormatter.ofPattern("dd-mm-yyyy");
for(final String line: lines) {
    final String[] columns = line.split(",");
    final LocalDate date = LocalDate.parse(columns[0], DATE_PATTERN);
    if(date.getMonth() == Month.JANUARY) {
        final double amount = Double.parseDouble(columns[1]);
        total += amount;
    }
    
}
```

### final 변수
지역 변수나 필드를 final 로 정의하기 때문에 이 변수에 값을 재할당할 수 없다.
코드에서 가능한 많은 변수를 final로 표시하면 어떤 객체의 상태가 바뀔 수 있고, 어떤 객체의 상태가 바뀔 수 없는지 명확하게 구분할 수 있다.
final 키워드를 적용한다고 해서 객체가 바뀌지 못하도록 강요하는 것은 아니다. final 필드로 가리키는 객체라도 가변상태를 포함한다.
추상메서드의 메서드 파라미터에 final을 사용하는 상황에서는 실제 구현이 없으므로 final 키워드의 의미가 무력화된다.

## 코드 유지보수성과 안티 패턴
- 특정 기능을 담당하는 코드를 쉽게 찾을 수 있어야 한다.
- 코드가 어떤 일을 수행하는지 쉽게 이해할 수 있어야 한다.
- 새로운 기능을 쉽게 추가하거나 기존 기능을 쉽게 제거할 수 있어야 한다.
- 캡슐화가 잘 되어 있어야 한다. 즉 코드 사용자에게는 세부 구현 내용이 감춰져 있으므로 사용자가 쉽게 코드를 이해하고, 기능을 바꿀 수 있어야 한다.

궁극적으로 개발자의 목표는 현재 만들고 있는 응용프로그램의 복잡성을 관리하는 것이다. 
안티 패턴(anti-pattern)
- 한개의 거대한 갓 클래스 때문에 코드를 이해하기 어렵다.
- 코드 중복때문에 코드가 불안정하고 변화에 쉽게 망가진다.

### 갓 클래스
한 개의 파일에 모든 코드를 구현하다 보면 결국 하나의 거대한 클래스가 탄생하면서 클래스의 목적이 무엇인지 이해하기 어려워진다. 이 거대한 클래스가 모든 일을 수행하기 때문이다.
이런 문제를 갓 클래스 안티 패턴이라 부른다.

### 코드 중복
여러 곳에 코드가 중복되어 있으면 기존의 기능을 바꾸기가 어렵다.

코드를 간결하게 유지하는 것도 중요하지만, KISS 원칙을 남용하지 않아야 한다.

## 단일 책임 원칙
단일 책임 원칙(이하 SRP)은 쉽게 관리하고 유지보수하는 코드를 구현하는데 도움을 주는 포괄적인 소프트웨어 개발 지침이다.
다음 두 가지를 보완하기 위해 SRP를 적용한다.
- 한 클래스는 한 기능만 책임진다.
- 클래스가 바뀌어야 하는 이유는 오직 하나여야 한다.
SRP는 일반적으로 클래스와 메서드에 적용한다. SRP는 한 가지 특정 동작, 개념, 카테고리와 관련된다. SRP를 적용하면 코드가 바뀌어야 하는 이유가 한 가지로 제한되므로 더 튼튼한 코드를 만들 수 있다.

메서드를 구현할 때는 놀람 최소화 원칙을 따라야 한다. 그래야 코드를 보고 무슨일이 일어나는지 명확히 이해할 수 있기 때문이다.
- 메서드가 수행하는 일을 바로 이해할 수 있도록 자체 문서화를 제공하는 메서드명을 사용한다.
- 코드의 다른 부분이 파라미터의 상태에 의존할 수 있으므로 파라미터의 상태를 바꾸지 않는다.

## 응집도
소프트웨어 엔지니어링과 관련해 응집도는 코드 구현에서 중요한 특성이다.
응집도는 서로 어떻게 관련되어 있는지를 가리킨다. 정확히 말하자면 응집도는 클래스나 메서드의 책임이 서로 얼마나 강하게 연결되어 있는지를 측정한다. 즉 어떤 것이 여기저기에 모두 속해 있는지를 말한다. 응집도는 소프트웨어의 복잡성을 유추하는 데 도움을 준다. 높은 응집도는 개발자의 목표이고, 누구나 쉽게 코드를 찾고, 이해하고, 사용할 수 있도록 만들고 싶어 한다.
보통 응집도 개념은 클래스(클래스 수준 응집도)에 적용하지만, 이를 메서드(메서드 수준 응집도)에도 적용할 수 있다.

### 클래스 수준 응집도
실무에서는 일반적으로 다음과 같은 여섯 가지 방법으로 그룹화한다.
**기능**
함께 사용하는 메서드를 그룹화하면 찾기도 쉽고 이해하기도 쉬우므로 응집도를 높인다. 다만 기능 응집은 한 개의 메서드를 갖는 클래스를 너무 과도하게 만들려는 경향이 발생할 수 있다는 약점이 있다. 간단한 클래스를 과도하게 만들면 그만큼 생각해야 할 클래스가 많아지므로 코드가 장황해지고 복잡해진다.
**정보**
같은 데이터나 도메인 객체를 처리하는 메서드를 그룹화하는 방법도 있다. 정보 응집은 여러 기능을 그룹화하면서, 필요한 일부 기능을 포함하는 클래스 전체를 디펜던시로 추가한다는 약점이 있다.
**유틸리티**
때로는 관련성이 없는 메서드를 한 클래스로 포함시켜야 한다. 특히 메서드가 어디에 속할지 결정하기 어려울 때는 유틸리티 클래스에 추가하기도 한다.
유틸리티 클래스 사용은 낮은 응집도로 이어지므로 자제해야 한다. 메서드가 서로 연관성이 없으므로 클래스 전체의 기능을 추론하기가 어렵다. 
**논리**
논리로 그룹화를 하면 본질적으로 다르기 때문에 서로 관련이 없다. 이렇게 그룹화를 하면, 클래스는 논리로 그룹화된 책임들을 갖게 되므로 이전에 배웟던 SRP를 위배한다. 결과적으로 이 방법은 권장하지 않는다.
**순차**
입출력이 순차적으로 흐르는것을 순차 응집이라 부른다. 순차 응집은 여러 동작이 어떻게 함께 수행되는지 쉽게 이해할 수 있다. 실전에서 순차 응집을 적용하면 한 클래스를 바꿔야 할 여러 이유가 존재하므로 SRP를 위배한다. 따라서 각 책임을 개별적으로 응집된 클래스로 분리하는 것이 더 좋은 방법이다.
**시간**
시간 응집클래스는 여러 연산 중 시간과 관련된 연산을 그룹화 한다.

### 메서드 수준 응집도
응집도 원칙은 클래스뿐만 아니라 메서드에도 적용할 수 있다. 메서드가 다양한 기능을 수행할 수록 메서드가 어떤 동작을 하는지 이해하기가 점점 어려워진다. 즉 메서드가 연관이 없는 여러 일을 처리한다면 응집도가 낮아진다. 응집도가 낮은 메서드는 여러 책임을 포함하기 때문에 각 책임을 테스트하기가 어렵고, 메서드의 책임도 테스트하기가 어렵다. 일반적으로 클래스나 메서드 파라미터의 여러 필드를 바꾸는 if/else블록이 여러 개 포함되어 있다면, 이는 응집도에 문제가 있음을 의미하므로 응집도가 높은 더 작은 조각으로 메서드를 분리해야 한다.

## 결합도
코드를 구현할 때 고려해야 할 또 다른 중요한 특성으로 결합도가 있다. 응집도는 클래스, 패키지, 메서드 등의 동작이 얼마나 관련되어 있는가를 가리키는 반면, 결합도는 한 기능이 다른 클래스에 얼마나 의존하고 있는지를 가늠한다. 결합도는 어떤 클래스를 구현하는 데 얼마나 많은 지식(다른 클래스)을 참조했는가로 설명할 수 있다. 더 많은 클래스를 참조했다면 기능을 변경할 때 그만큼 유연성이 떨어진다. 어떤 클래스의 코드를 바꾸면 이 클래스에 의존하는 모든 클래스가 영향을 받는다.
결합도는 코드가 서로 어떻게 의존하는지와 관련이 있는 척도다.

보통 코드를 구현할 때는 결합도를 낮춰야 한다. 이는 코드의 다양한 컴포넌트가 내부와 세부 구현에 의존하지 않아야 함을 의미한다. 반대로 높은 결합도는 무조건 피해야 한다.

## 테스트
### 테스트 자동화
테스트 자동화의 장점
**확신**
소프트웨어가 규격 사양과 일치하며 동작하는지를 테스트해 고객의 요구 사항을 충족하고 있다는 사실을 더욱 확신할 수 있다. 테스트 규격 사양과 결과를 고객에게 증거로 제공할 수도 있다. 즉 테스트가 고객의 사양이 된다.

**변화에도 튼튼함 유지**
자동화된 테스트 스위트가 있다면 바꾼 코드로 인해 새로운 버그가 발생하지 않음을 확인하는 데 큰 도움이 된다.

**프로그램 이해도**
테스트 자동화는 소스코드의 프로젝트에서 다양한 컴포넌트가 어떻게 동작하는지 이해하는 데 도움을 준다. 테스트는 다양한 컴포넌트의 디펜던시와 이들이 어떻게 상호작용하는지를 명확하게 드러낸다. 따라서 소프트웨어의 전체 개요를 빨리 파악할 수 있다.

### 제이유닛 사용하기
**테스트 메서드 정의하기**
메이븐과 그레이들 빌드 도구에서는 src/main/java에 코드를 저장하고 src/test/java에 테스트 클래스를 저장하는 것이 기본 규칙이다.
- 보통 테스트 클래스명에는 Test라는 접미어를 붙이는 것이 관습이다.
- 테스트 메서드의 구현 코드를 보지 않고도 무엇을 테스트하는지 쉽게 알 수 있도록 서술적인 이름을 붙이는 것이 좋다.
- @Test를 테스트 메서드에 추가한다. 이 애너테이션으로 해당 메서드가 유닛 테스트의 실행 대상임을 지정한다.

### 코드 커버리지
코드커버리지는 테스트 집합이 소프트웨어의 소스코드를 얼마나 테스트했는가를 가리키는 척도다. 커버리지가 높을수록 예상하지 못한 버그가 발생할 확률이 낮아지므로 되도록 커버리지를 높이는 것을 목표로 삼아야 한다.
몇 퍼센트의 커버리지가 좋은지는 정해지지 않았지만, 보통 70에서 90퍼센트를 목표로 정할 것을 권한다.
코드 커버리지가 높다고해서 소프트웨어를 잘 테스트하고 있음을 의미하는 것은 아니다. 코드 커버리지는 테스트하지 않은 부분이 남아 있음을 알려주는 역할에 지나지 않기 때문에 테스트의 품질과는 아무 관련이 없다.

자바에서는 자코코, 에마, 코베르투라 같은 코드 커버리지 도구를 많이 사용한다. 사람들은 얼마나 많은 구문의 코드를 커버했는지를 의미하는 구문 커버리지에 대해 자주 이야기 한다. 이 기법에는 분기문(if, while, for)을 한 구문으로 취급해버리는 치명적인 약점이 있다. 사실 분기문에는 가능한 여러 개의 경로가 있기 때문이다. 따라서 구문 커버리지보다 각 분기문을 확인하는 분기 커버리지를 사용하는 것이 좋다.
