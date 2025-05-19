# variable arguments

**관련 헤더**

- stdarg.h
    - 자료형
        - va_list : 가변배열의 포인터를 받는 자료형
    - 함수
        - va_start : 가변배열의 시작을 설정하는 함수
            - va_start(va_list 변수, 함수의 첫번째 인자);
            - 이렇게 초기화 된 함수는 함수의 두번째인자, 즉 가변배열의 첫번째를 가리킨다
        - va_arg : 가변배열에서 자료를 추출해 내는 함수
            - va_arg(va_list 변수, 추출해 낼 자료형);
                - ex) va_arg(ap, int);
            - va_list 에서 자료형 만큼을 읽어 반환하고 va_list 를 해당 자료형의 크기만큼 뒤로 옮긴다
                - 이때 float 는 double 로, short 와 char 은 int 로 받는다
                - 왜? byte padding 때문에
        - va_end : va_list 를 null 로 초기화
            - va_end(va_list 변수);

**가변 인자 함수의 선언**

- 반환형 함수이름(첫번째 인자, …);
- 첫번째 인자 뒤에 … 을 넣어 가면 인자 함수임을 명시한다

**사용**

- va_list 변수를 선언한 다음 va_start 에서 함수의 첫 번째 매개변수를 이용해 초기화 한다
- 이때 함수의 첫 매개변수를 사용해 뒤에 나올 가변배열의 인자들에 대해 일종의 flag 를 준다
- 함수의 첫 매개변수의 사용 예
    - int 형, 즉 숫자가 나오면 뒤에나올 매개변수들의 숫자를 표시할 수 있다
    - char * 형, 즉 문자열이 나오면 뒤에나올 매개변수들의 자료형을 표시할 수 있다
- va_list 변수에서 va_arg 로 데이터를 뽑아 사용한다
- 사용이 끝나면 va_end 로 va_list 변수를 초기화 해 준다

```c
va_list ap;int i;int s;va_start(ap, argv);i = -1;while (++i < argc){    s = va_arg(ap, int);    printf("%d ", s);}va_end(ap);printf("\n");
```