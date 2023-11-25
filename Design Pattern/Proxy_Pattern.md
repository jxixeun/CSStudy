# :dizzy: Proxy 패턴

**:bulb: Proxy = 대리인**

본인 객체 대신 대리인 객체가 일부 일을 처리해주는 패턴

**Proxy 패턴에서는 Proxy가 대리인이 되어 가능한 한 처리를 대신한다.**

초기화에 시간이 많이 걸리는 대규모 시스템의 경우, 시작 시점에 이용하지 않는 기능까지 전부 초기화해 버리면 애플리케이션 시작 시간이 길어진다. 실제로 기능을 사용하는 단계가 되었을 때 초기화하는 편이 더 좋다.

Proxy 패턴의 예로는 이미지가 삽입된 문서 편집기가 있는데, 그래픽 객체를 생성하려면 이미지 파일을 불러오는 등 시간이 오래걸릴 수 있기 때문에 문서를 열 때 모든 그래픽 객체를 생성하는 것은 시간 낭비이다. 각각의 그래픽 객체를 화면에 표시할 때가 되었을 때 생성하는 것이 좋다. Proxy 패턴은 이러한 경우에 사용된다.

## :dizzy: Proxy 패턴의 등장 인물

### Subject (본인)

Proxy와 RealSubject를 동일시하기 위한 인터페이스(API)를 정의한다. 

Subject 덕분에 Client는 Proxy와 RealSubject의 차이를 의식할 필요가 없다.

예제에서 Printable 인터페이스가 Subject이다.

### Proxy (대리인)

Client의 요청을 **최대한** 처리한다. 자신이 처리할 수 없을 경우, RealSubject에 처리를 맡긴다. 

정말 RealSubject가 필요할 때 RealSubject를 생성한다. Proxy는 Subject에 정의된 인터페이스(API)를 구현한다.

예제에서 PrinterProxy가 Proxy이다.

### RealSubject (실제 본인)

대리인인 Proxy가 감당할 수 없을 때 등장하는 것이 RealSubject이다. Proxy와 같이 Subject에서 정의한 인터페이스(API)를 구현한다.

예제에서 Printer가 RealSubject이다.

### Client (의뢰인)

Proxy 패턴을 이용한다. (GoF 책에서 Client는 Proxy 패턴에 포함되지 않는다.)

예제에서 Main이 Client 이다.

## :dizzy: 예제

### Subject

```java
public interface Printable {
    void setPrintName(String name);
    String getPrinterName();
    void print(String string);
}
```

### RealSubject

```java
public class Printer implements Printable {

    private String name;

    public Printer(String name) {
        this.name = name;
        heavyJob("Print 인스턴스 (" + name + ") 생성 중");
    }

    @Override
    public void setPrintName(String name) {
        this.name = name;
    }

    @Override
    public String getPrinterName() {
        return name;
    }

    @Override
    public void print(String string) {
        System.out.println("=== " + name + " ===");
        System.out.println(string);
    }

    private void heavyJob(String msg) {
        System.out.println(msg);
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("생성 완료");
    }
}
```

### Proxy

```java
public class PrinterProxy implements Printable {

    private String name;
    private Printer printer;

    public PrinterProxy() {
        this.name = null;
        this.printer = null;
    }

    public PrinterProxy(String name) {
        this.name = name;
        this.printer = null;
    }

    @Override
    public void setPrintName(String name) {
        if (printer != null){
            printer.setPrintName(name);
        }
        this.name = name;
    }

    @Override
    public String getPrinterName() {
        return name;
    }

    @Override
    public void print(String string) {
        realize();
        printer.print(string);
    }

    private synchronized void realize() {
        if (printer == null){
            printer = new Printer(name);
        }
    }
}
```

### Client

```java
public class Main {
    public static void main(String[] args) {
        Printable printer = new PrinterProxy("Alice");
        System.out.println("현재 이름은 " + printer.getPrinterName() + " 입니다.");
        printer.setPrintName("Bob");
        System.out.println("현재 이름은 " + printer.getPrinterName() + " 입니다.");
        printer.print("Hello, World");
    }
}
```

### 실행 결과
```
현재 이름은 Alice 입니다.
현재 이름은 Bob 입니다.
Print 인스턴스 (Bob) 생성 중
생성 완료
=== Bob ===
Hello, World
```

위 예제를 보면 실제로 Printer가 필요한 경우 (print 메서드를 사용할 때)를 제외하고 다른 일들을 모두 Proxy가 대신 처리하고 있다.

일을 대신 처리함으로써 무거운 작업들을 하지 않고, 실행시간을 줄이고 있다. 또한 객체를 실제 사용할 때 생성함으로써, 애플리케이션의 시작 시간을 줄이고 있다.


## :dizzy: 대리인과 본인을 나누는 이유는?

Proxy를 생성할 필요 없이 RealSubject에 처음부터 지연 평가 기능(필요할 때 인스턴스를 생성하는 기능)을 넣을 수도 있다. 그러나 Proxy와 RealSubject를 분리함으로써 프로그램이 부품화되면 개별적으로 수정할 수 있다.

Proxy의 구현을 바꾸면, 인터페이스에서 선언된 메소드 중 무엇을 대리인이 처리하고 무엇을 본인이 처리할 지 변경할 수 있다. Proxy를 계속 변경하더라도 RealSubject는 수정할 필요가 없다.

지연 평가를 하지 않으려면 Client가 Proxy를 생성하지 않고 RealSubject를 생성하면 된다. Proxy와 RealSubject는 모두 Subject를 구현하기 때문에 Client는 두 가지를 전환해서 사용할 수 있다.

이 경우 Proxy 클래스를 '투과적'이라고 한다. Client와 RealSubject 사이에 Proxy가 존재해도 문제가 없다.

## :dizzy: HTTP 프록시

HTTP 프록시는 HTTP 서버와 HTTP 클라이언트 사이에서 웹 페이지 캐싱을 하는 소프트웨어이다.

캐싱의 경우, 웹 브라우저(Client)가 웹 페이지를 표시할 때 실제 웹 서버(Real Subject)에 접속해서 가져오는 것이 아니라, HTTP 프록시(Proxy)가 캐싱해 놓은 페이지를 대신 가져온다. 

## :dizzy: 다양한 Proxy

### 가상 프록시 (Virtual Proxy)
  
  위의 설명한 Proxy 패턴이다. 실제로 인스턴스가 필요한 시점에서 생성 및 초기화한다.
  
### 원격 프록시 (Remote Proxy)
  
  RealSubject가 네트워크 저편에 있음에도 불구하고 마치 자기 옆에 있는 것처럼 메소드를 호출할 수 있다. Java RMI 등이 여기에 해당한다.
  
### 보호 프록시 (Access Proxy)
  
  RealSubject의 기능에 대한 접근 제한을 설정한다. 지정된 사용자라면 메소드 호출을 허가하지만, 나머지는 오류가 되도록 처리하는 프록시이다.

## :dizzy: Spring AOP가 구현되는 원리

Proxy는 RealSubject의 일을 대신 처리한다. 대신 처리할 때, 다른 작업을 추가하여 RealSubject의 코드 수정 없이 기능을 추가할 수 있다.

### Subject

```java
public interface Greetable {
    void greet();
}
```

### RealSubject

```java
public class Greet implements Greetable{
    @Override
    public void greet() {
        System.out.println("Hello!");
    }
}
```

RealSubject는 Hello! 를 출력하는 역할을 한다. 이 때 RealuSubject의 수정 없이 앞 뒤로 로그를 출력하고 싶다. 이를 위해 Proxy를 사용할 수 있다.

### Proxy

```java
public class GreetProxy implements Greetable {

    private Greet realSubject;

    public GreetProxy() {
        realSubject = new Greet();
    }

    @Override
    public void greet() {
        System.out.println("=== 실행 전 로그를 찍습니다. ===");
        realSubject.greet();
        System.out.println("=== 실행 후 로그를 찍습니다. ===") ;
    }
}
```

### Client

```java
public class Main {
    public static void main(String[] args) {
        Greetable greet = new GreetProxy();
        greet.greet();
    }
}
```

### 실행 결과
```
=== 실행 전 로그를 찍습니다. ===
Hello!
=== 실행 후 로그를 찍습니다. ===
```

이처럼 RealSubject의 수정 없이 앞 뒤로 로그를 출력하도록 변경했다. 이러한 식으로 AOP가 구현된다고 보면 될 거 같다. 

(뇌피셜로 구현되었다. 실제로는 훨씬 비교도 안되게 복잡하다!)
