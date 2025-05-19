# 정적 static 변수와 메소드

```
class Singleton {
    private static Singleton one;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if(one==null) {
            one = new Singleton();
        }
        return one;
    }
}
```

**static 변수**

- 메모리 관리 측면에서 유리하다
    - 변수에 static 이 붙어있으면 해당 변수는 여러번 호출되지 않는다
    - 만약 같은 클래스의 객체를 여러번 생성할 일이 있을 때, 객체변수는 객체가 생길때마다 할당되지만 static 으로 설정된 변수는 한번만 할당한다
- 공유의 목적으로 사용이 가능하다
    - 같은 클래스로 만들어진 객체들에서 static 변수는 공유된다
    - 같은 클래스로 만들어진 두 객체에사 한쪽객체릐 정적변수를 수정하면 다른 변수에서도 수정이 이루어진다

**static 메소드**

- 메소드앞에 static 을 붙이면 static 메소드가 된다
- static 메소드는 객체를 생성하지 않고 직접 호출할 수 있다
    - 새 객체를 생성하지 않고 class.method() 처럼 직접 호출하여 쓸 수 있다
- 이때 스태틱 메소드 안에서는 객체변수의 접근이 불가능하다

**싱글톤 패턴**

- 간혹 단 하나의 객체만을 생성해야 하는 경우가 있다
- 하나의 객체만 만들수 있도록 하는걸 싱글톤 이라고한다

```
class Singleton {
    private static Singleton one;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if(one==null) {
            one = new Singleton();
        }
        return one;
    }
}
```

- 생성자에 private 를 걸어 해당 클래스가 아니면 객체생성을 못하도록 막는다
- 이렇게 하면 다른곳에서 해당 객체를 만들지 못한다
- 그 다음 static 메소드를 만들어 새로운 객체 생성을 가능하도록 한다
- 해당 클래스는 private 로 막혀있지만 static 메소드는 사용이 가능하다
- 스태틱메소드를 사용하면 미리 선언해 둔 one 에 객체가 생성되고 다음부터는 one 은 null 이 아니니 객체 생성이 불가능하게된다
- 이 예제의 싱글톤은 멀티쓰레드 환경에서는 작동하지않는다