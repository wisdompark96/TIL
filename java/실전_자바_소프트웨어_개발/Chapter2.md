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
