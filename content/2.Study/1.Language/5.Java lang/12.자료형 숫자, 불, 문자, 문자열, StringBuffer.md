# 자료형 숫자, 불, 문자, 문자열, StringBuffer

변수명은 숫자로 시작할 수 없으며

_ 와 $ 를 제외한 어떤 특수문자도 올 수 없으며

자바의 키워드는 변수명으로 사용할 수 없다

**자료형(Type)**

- int
- long
- double
- boolean
- char
- String
- StringBuffer
- List
- Map
- Set

**사용자정의자료형**

- 직접 자료형을 만들어 사용할 수 있다
- ex)

```
class Animal{ // Animal 이라는 클래스를 만들고
}
.
.
Animal cat; // Animal 자료형 변수를 선언한다
```

### **자료형 숫자**

**정수**

- int : -2147483648 ~ 2147483647
- long : -9223372036854775808 ~ 9223372036854775807
    - int 의 한계를 넘어가는 큰/작은 숫자를 입력할 때
    - long num = 8764827384923849L;
    - 과같이 숫자 맨 뒤에 L 또는 l (소문자L) 을 붙여주어야 한다
    - 소문자 l 은 헷갈릴 일이 많아 잘 사용하지 않는다

**실수**

- float : -3.4*10^38 ~ 3.4*10^38
- double : -3.4*10^308 ~ 3.4*10^308
    - 실수형의 디폴트는 double 이므로 float 형을 사용할때는
    - float pi = 3.14F;
    - 와 같이 F 또는 f 를 붙여주어야한다

**8진수와 16진수**

- 정수형 int 를 사용한다
- 8진수는 맨 앞에 0 을
- 16진수는 맨 앞에 0x 를 붙인다
    - int octal = 023 ;
    - int hex = 0xC ;

**사칙연산**

- + - * / % 등 똑같이 사용
- 증감연산자 ++, –
    - i++. i– 등으로 사용
    - i 에 1을 더하거나 빼 준다
    - i++ 는 i 를 사용한 다음 1을 더해주고
    - ++i 는 i 에 1을 더해준 다음 사용한다

### **자료형 불**

**불, 불리언, boolean**

- treu, false 두개의 값만 대입 가능하다
- >, <, ==, 등 연산의 결과값으로 저장이 가능하다

### **자료형 문자**

**문자 한개 (알파벳 한개)**

- char
- 대입하는 문자값은 ’ 로 감싸주어야한다
- char a1 = ‘a’;

**문자열**

- String
- 문장을 뜻한다
- 대입하는 문자열은 " 로 감싸주어야한다
- String a = “something” ;
- String a = new String(“something”)
- 두가지 방법으로 사용 가능하나 첫번째 방식으로 만드는게 가독성이 좋다
- 두번째 방법은 객체를 생성할 때 사용한다
- 한번 만들면 불변하다 = immutable
- **문자열 내장 메소드**
    - 내장 메소드는 a.indexOf() ~ 라는 식으로 사용하면 된다
    - euqals - 두 객체가 서로 같은지 비교
    - indexOf - 특정 문자열이 시작하는 인덱스 리턴
    - contains - 포함여부
    - charAt - 해당 인덱스의 문자(char)를 리턴
    - replaceAll - 특정 문자열을 다른문자열로 바꿈
    - substring - 특정 문자열을 뽑아냄(=슬라이싱)
    - toUpperCase / toLowerCase - 모두대문자/소문자로
    - split - 해당 구분자로 문자열을 분리해 배열로 만듬
- **문자열 포매팅**
    - 문자열을 만들떄 변수를 대입한다
        - String.format(“I have %d apple”, 3) // I have 3 apple
        - String.format(“I have %s”, apple) // I have apple
    - 대체할 문자열, 숫자 등은 변수로 대입이 가능하다
        - int num = 3;
        - String.format(“I have %d apple”, num) // I have 3 apple
    - 여러개의 값 넣기
        - 여러개의 파라미터로 전달하면 된다
        - String.format(“I have %d %s”, 3. apple) // I have 3 apple
    - 문자열 포멧 코드
        - %s : 문자열
        - %c : 문자 1개
        - %d : 정수
        - %f : 부동소수
        - %o : 8진수
        - %x : 16진수
        - %% : 기호 % 그 자체 - 포메팅과 % 를 동시에 사용할 때
            - 이때 %s 를 사용하면 뒤의 어떤 변수를 사용하든 문자열로 변환해 대입한다
    - 포멧 코드의 응용
        - %10s - 해당 문자열 왼쪽으로 10 칸의 공백이 들어간다. 음수일때는 오른쪽으로 공백이 들어간다
        - %.4f - 소수점 4자리 까지만 보여준다
    - System.out printf
        - 일반적으로 System.out println 메소드를 사용해
            - System.out println(String.format(“some %s thing”, 변수) 로 사용하지만
        - 포메팅한 문자열은 System.out printf 메소드를 사용해
            - System.out printf(“some %s thing”, 변수) 로 사용하면 String.format 메소드 없이 같은 효과를 낼 수 있다

**StringBuffer**

- 문자열을 추가, 변경할때 사용
- StringBuffer sb = new StringBuffer();
- sb.append(“something”); . . .
- String 에 + 를 하여 연산을 해도 append 와 같은 결과를 얻을 수 있다
- String 에 비해 상대적으로 무거운편이기에 문자열의 변동이 많고 적고를 구분하여 사용한다
- 변경이 가능하다 = mutable
- **StringBuilder**
    - StringBuffer 와 같은 역할을 한다
    - StringBuffer 는 멀티스레드 환경에 안전하고 StringBuilder 는 성능이 우수하다
- **내장 메소드**
    - insert - 원하는 인덱스에 문자열 추가
    - append - 문자열 추가
    - substring - 슬라이싱
    - toString - 문자열로 변경

### **원시자료형**

- int, long, double, float, boolean, char 은 원시자료형이다
- 원시자료형은 new 값으로 키워드를 만들 수 없고 리터럴 형태로만 값을 대입 할 수 있다
- String 은 원시자료형은 아니나 특수하게 리터럴 방식으로 사용이 가능하다
- 각 원시자료형은 각각에 대입하는 Wrapper class 가 존재한다
- int - Integer
- long - Long
- double - Double
- float - Float
- boolean - Boolean
- char - Cha
- 원시자료형 대신 Wrapper class 를 사용하면 값 대신 객체를 사용하게 되어 코드를 객체중심적으로 짤 때 유리하다
- 멀티쓰레딩 환경에서 동기화를 진행하는 경우 Wrapper class 사용이 필요하다