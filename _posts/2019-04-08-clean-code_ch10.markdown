---
layout: post
title: "Ch10. 클래스"
subtitle: 클래스의 결합도를 낮추고 응집도를 높이자
date: 2019-04-08 23:28:00 +0900
categories: CleanCode
tags: CleanCode
---

# 클래스
---

#### 클래스 체계
클래스를 정의하는 표준 자바 관례에 따르면, 가장 먼저 `변수`  목록이 나온다. 
그 다음에는 `공개 함수` 가 나온다. 
`비공개 함수` 는 자신을 호출하는 공개 함수 직후에 넣는다. 
즉, **추상화 단계가 순차적으로 내려간다.**

##### 캡슐화
변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다. 하지만 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

---

#### 클래스는 작아야 한다!
* 클래스는 크기가 작고 또 작아야 한다. 
* 클래스의 크기를 측정하는 척도는 클래스가 맡은 **책임**이다.
* 클래스 이름은 해당 클래스의 책임을 기술해야 한다.

##### 단일 책임 원칙 *SRP*
`Single Responsibility Principle` : **클래스나 모듈을 변경할 이유가 단 하나뿐이어야 한다.**
> 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다. 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.

##### 응집도 *Cohesion*
* 클래스는 인스턴스 변수 수가 작아야 한다. 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다. 일반적으로 **메서드가 변수를 더 많이 사용할수록** 메서드와 클래스는 응집도가 더 높다.
* `응집도` 가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미이다.
* `함수를 작게, 매개변수 목록을 짧게` 라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다. 이는 십중팔구 **새로운 클래스로 쪼개야 한다는 신호**다.

##### 응집도를 유지하면 작은 클래스 여럿이 나온다
큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다. 그러면서 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

```java
// 큰 함수를 작은 함수/클래스 여럿으로 쪼개보는 예제

public class PrintPrimes {

        public static void main(String[] args) {
                final int M = 1000;
                final int RR = 50;
                final int CC = 4;
                final int WW = 10;
                final int ORDMAX = 30;
                int P[] = new int[M + 1];
                int PAGENUMBER;
                int PAGEOFFSET;
                int ROWOFFSET;
                int C;
                int J;
                int K;
                boolean JPRIME;
                int ORD;
                int SQUARE;
                int N;
                int MULT[] = new int[ORDMAX + 1];
                
                J = 1;
                K = 1;
                P[1] = 2;
                ORD = 2;
                SQUARE = 9;
        
                while (K < M) {
                        do {
                                J = J + 2;
                                if (J == SQUARE) {
                                        ORD = ORD + 1;
                                        SQUARE = P[ORD] * P[ORD];
                                        MULT[ORD - 1] = J;
                                }
                                N = 2;
                                JPRIME = true;
                                while (N < ORD && JPRIME) {
                                        while (MULT[N] < J)
                                                MULT[N] = MULT[N] + P[N] + P[N];
                                        if (MULT[N] == J)
                                                JPRIME = false;
                                        N = N + 1;
                                }
                        } while (!JPRIME);
                        K = K + 1;
                        P[K] = J;
                }
                {
                        PAGENUMBER = 1;
                        PAGEOFFSET = 1;
                        while (PAGEOFFSET <= M) {
                                System.out.println("The First " + M + " Prime Numbers --- Page " + PAGENUMBER);
                                System.out.println("");
                                for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
                                        for (C = 0; C < CC;C++)
                                                if (ROWOFFSET + C * RR <= M)
                                                        System.out.format("%10d", P[ROWOFFSET + C * RR]);
                                        System.out.println("");
                                }
                                System.out.println("\f"); PAGENUMBER = PAGENUMBER + 1; PAGEOFFSET = PAGEOFFSET + RR * CC;
                        }
                }
        }
}
```

위 코드는 함수가 하나뿐이고, 들여쓰기가 심하고, 이상한 변수가 많고, 구조가 빡빡하게 결합되어있다.
  
아래 코드는 원래 프로그램의 한 클래스가 가지고 있던 모든 책임을 각 책임의 클래스와 함수로 분리했다. 그리고 좀 더 의미 있는 이름을 부여한 결과다.

```java
public class PrimePrinter {

        public static void main(String[] args) {

                final int NUMBER_OF_PRIMES = 1000;
                int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
                
                final int ROWS_PER_PAGE = 50;
                final int COLUMNS_PER_PAGE = 4;
                RowColumnPagePrinter tablePrinter =
                        new RowColumnPagePrinter(ROWS_PER_PAGE,
                                                COLUMNS_PER_PAGE,
                                                "The First " + NUMBER_OF_PRIMES + " Prime Numbers");
                tablePrinter.print(primes);
        }
}
```

```java
public class RowColumnPagePrinter {

        private int rowsPerPage;
        private int columnsPerPage;
        private int numbersPerPage;
        private String pageHeader;
        private PrintStream printStream;
        
        public RowColumnPagePrinter(int rowsPerPage, int columnsPerPage, String pageHeader) {
                this.rowsPerPage = rowsPerPage;
                this.columnsPerPage = columnsPerPage;
                this.pageHeader = pageHeader;
                numbersPerPage = rowsPerPage * columnsPerPage;
                printStream = System.out;
        }
        
        public void print(int data[]) {
                int pageNumber = 1;
                for (int firstIndexOnPage = 0 ;
                        firstIndexOnPage < data.length ;
                        firstIndexOnPage += numbersPerPage) {
                        int lastIndexOnPage =  Math.min(firstIndexOnPage + numbersPerPage - 1, data.length - 1);
                        printPageHeader(pageHeader, pageNumber);
                        printPage(firstIndexOnPage, lastIndexOnPage, data);
                        printStream.println("\f");
                        pageNumber++;
                }
        }
        
        private void printPage(int firstIndexOnPage, int lastIndexOnPage, int[] data) {
                int firstIndexOfLastRowOnPage =
                firstIndexOnPage + rowsPerPage - 1;
                for (int firstIndexInRow = firstIndexOnPage ;
                        firstIndexInRow <= firstIndexOfLastRowOnPage ;
                        firstIndexInRow++) {
                        printRow(firstIndexInRow, lastIndexOnPage, data);
                        printStream.println("");
                }
        }
        
        private void printRow(int firstIndexInRow, int lastIndexOnPage, int[] data) {
                for (int column = 0; column < columnsPerPage; column++) {
                        int index = firstIndexInRow + column * rowsPerPage;
                        if (index <= lastIndexOnPage)
                                printStream.format("%10d", data[index]);
                }
        }

        private void printPageHeader(String pageHeader, int pageNumber) {
                printStream.println(pageHeader + " --- Page " + pageNumber);
                printStream.println("");
        }
                
        public void setOutput(PrintStream printStream) {
                this.printStream = printStream;
        }

}
```

```java
public class PrimeGenerator {

        private static int[] primes;
        private static ArrayList<Integer> multiplesOfPrimeFactors;

        protected static int[] generate(int n) {
                primes = new int[n];
                multiplesOfPrimeFactors = new ArrayList<Integer>();
                set2AsFirstPrime();
                checkOddNumbersForSubsequentPrimes();
                return primes;
        }

        private static void set2AsFirstPrime() {
                primes[0] = 2;
                multiplesOfPrimeFactors.add(2);
        }
        
        private static void checkOddNumbersForSubsequentPrimes() {
                int primeIndex = 1;
                for (int candidate = 3 ; primeIndex < primes.length ; candidate += 2) {
                        if (isPrime(candidate))
                                primes[primeIndex++] = candidate;
                }
        }

        private static boolean isPrime(int candidate) {
                if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
                        multiplesOfPrimeFactors.add(candidate);
                        return false;
                }
                return isNotMultipleOfAnyPreviousPrimeFactor(candidate);
        }

        private static boolean isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
                int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
                int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
                return candidate == leastRelevantMultiple;
        }
        
        private static boolean isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
                for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
                        if (isMultipleOfNthPrimeFactor(candidate, n))
                                return false;
                }
                return true;
        }
        
        private static boolean isMultipleOfNthPrimeFactor(int candidate, int n) {
                return candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
        }
        
        private static int smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
                int multiple = multiplesOfPrimeFactors.get(n);
                while (multiple < candidate)
                        multiple += 2 * primes[n];
                multiplesOfPrimeFactors.set(n, multiple);
                return multiple;
        }
}
```

결과적으로 프로그램의 길이가 늘어났다. 그 이유는 다음과 같다.
1. 리팩터링한 프로그램은 좀 더 길고 **서술적인 변수 이름**을 사용한다.
2. 리팩터링한 프로그램은 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
3. 가독성을 높이고자 공백을 추가하고 **형식**을 맞추었다.

리팩토링시 작업 순서는 다음과 같다.
1. 원래 프로그램의 정확한 동작을 검증하는 `테스트 슈트` 를 작성한다.
2. 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드를 변경한다.
3. 코드를 변경할 때마다 **테스트**를 수행해 원래 프로그램과 동일하게 동작하는지 확인한다.

---

#### 변경하기 쉬운 클래스
- 깨끗한 시스템은 클래스를 체계적으로 정리해 `변경` 에 수반하는 위험을 낮춘다.
- 클래스 일부에서만 사용되는 비공개 메서드는 코드를 개선할 잠재적인 여지를 시사한다.
- 새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다. 
이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지는 않는다.
- 클래스를 책임별로 분리하면 클래스 설계 원칙인 `SRP` 뿐 아니라 `OCP` 도 지원한다.

`Open-Closed Principle` : **클래스는 확장에 개방적이고 수정에 폐쇄적이어야한다.**

##### 변경으로부터 격리
요구사항은 변하기 마련이다. 따라서 코드도 변하기 마련이다. 
상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다. 
그래서 `인터페이스`와 `추상 클래스`를 사용해 구현이 미치는 영향을 격리한다.

```java
// Portfolio 클래스를 구현하자. 그런데 이 클래스는 외부 TokyoStockExchange API를 사용해 포트폴리오 값을 계산한다.
// API 특성 상 시세 변화에 영향을 많이 받아 5분마다 값이 달라지는 API로 테스트 코드를 짜기란 쉽지 않다.
// 그러므로 Portfolio에서 외부 API를 직접 호출하는 대신 StockExchange라는 인터페이스를 생성한 후 메서드를 선언하다.

public interface StockExchange {
    Money currentPrice(String symbol);
}

// StockExchange 인터페이스를 구현하는 TokyoStockExchange 클래스를 구현한다.
// 그리고 Portfolio 생성자를 수정해 StockExchange 참조자를 인수로 받는다.

public Portfolio {
    private StockExchange exchange;
    
    public Portfolio(StockExchange exchange) {
        this.exchange = exchange;
    }
}

// 이제 TokyoStockExchange 클래스를 흉내내는 테스트용 클래스를 만들 수 있다.
// 테스트용 클래스는 StockExchange 인터페이스를 구현하며 고정된 주가를 반환한다.
// 그러므로 정해져있는 값에 대한 테스트 코드를 작성할 수 있다.

public class PortfolioTest {

    private FixedStockExchangeStub exchange;
    
    private Portfolio portfolio;

    @Before
    protected void setUp() throws Exception {
        exchange = new FixedStockExchangeStub();
        exchange.fix("MSFT", 100);
        portfolio = new Portfolio(exchange);
    }
    
    @Test
    public void GivenFiveMSFTTotalShouldBe500() throws Exception {
        portfolio.add(5, "MSFT");
        Assert.assertEquals(500, portfolio.value());
    }
}
```

- 위와 같은 테스트가 가능할 정도로 시스템의 결합도를 낮추면 유연성과 재사용성도 더욱 높아진다. 
**결합도가 낮다는 소리는 각 시스템 요소가 다른 요소로부터 그리고 변경으로부터 잘 격리되어 있다**는 의미다. 
시스템 요소가 서로 잘 격리되어 있으면 각 요소를 이해하기도 더 쉬워진다.
- 결합도를 최소로 줄이면 자연스럽게 또 다른 클래스 원칙인 `DIP` 를 따르는 클래스가 나온다.
   
`Dependency Inversion Principle` : **클래스가 상세한 구현이 아니라 추상화에 의존해야 한다.**

<br>
##### Reference
- *CleanCode 애자일 소프트웨어 장인 정신*
