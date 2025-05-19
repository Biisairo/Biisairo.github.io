# 스마트 포인터 smart pointer

**스마트 포인터**

- c++ 에서 new 로 할당받은 메모리는 delete 로 해제해 주어야 한다
- 스마트 포인터는 포인터처럼 동작하는 클래스 템플릿으로 사용이 끝난 메모리를 자동으로 해제해준다

**사용법**

- new 키워드를 사용해 기본 포인터에 메모리를 할당해 준 다음 기본 포인터를 스마트 포인터에 할당 해 준다
- 정해진 수명이 끝난 스마트 포인터는 자동적으로 delete 키워드를 사용해 메모리를 반환해준다

**종류**

- memory 헤더에 정의되어있다
- unique_ptr
- shared_ptr
- weak_ptr

**unique_ptr**

- 객체의 소유권 개념
- 하나의 객체는 하나의 스마트 포인터만이 소유할 수 있다
- 해당 객체의 삭제는 소유한 스마트 포인터 만이 할 수 있다
- 객체의 소유권은 move 를 사용해 이전할 수 있지만 복사할 수는 없다
    - 대입연산자 ‘=’ 를 사용하면 오류를 일으킨다
- 소유권이 이전되면 기존의 unique_ptr 는 소유권을 잃는다
- 사용
    - unique_ptr<자료형>포인터이름(new …);
        - ex) unique_ptr
            
            ptr01(new int(5));
            
    - c++14 이후에는 make_unique() 함수를 사용해 인스턴스 생성을 할 수 있다
    - make_unique() 함수는 전달박은 타입의 객체를 생성하고 해당 객체를 가리키는 unique_ptr 을 반환한다
        - ex) Person 이라는 클래스를 정의했다고 가정했을 때
        - unique_ptr
            
            hong = make_unique(“name”, age);
            

**shared_ptr**

- 하나의 객체를 공유하는 포인터
- 특정 객체를 공유하는 스마트 포인터의 갯수를 참조한다(?)
- 참조하고 있는 포인터의 개수를 참조 횟수 (reference count) 라고 한다
- 참조 횟수는 특정 객체에 새로운 shared_ptr 가 추가될 때 마다 1 씩 증가한다
- 참조 횟수가 0 이 되면 delete 키워드로 객체를 반환해준다
- 사용
    - shared_ptr<자료형>포인터이름(new …);
    - auto 포인터이름(기존 shared_ptr 이름); // 복사생성자를 이용한 초기화
    - auto 포인터이름 = 기존 shared_ptr 이름; // 대입을 이용한 초기화
        - ex) shared_ptr
            
            ptr01(new int(5));
            
        - auto ptr02(ptr01); // 복사생성자를 이용한 초기화
        - auto ptr03 = ptr01; // 대입을 이용한 초기화
- 함수
    - use_count() : 현재 참조하고 있는 포인터의 수를 반환
        - ex) han.usea.count();
    - reset() : 스마트포인터 해제
        - ex)han.reset();
- make_shared() 함수를 사용하여 객체를 생성할 수 있다

**weak_ptr**

- 하나이상의 shared_ptr 가 참조하는 객체에 접근하지만 참조 수에는 포함되지 않는 스마트 포인터
- 참조 횟수를 기반하여 작동하는 shared_ptr 의 특성상 서로를 참조하면 참조 횟수가 0 이 되지 않는 순환 참조 (circular reference) 가 된다
- 이를 방지하기 위해 weak_ptr 을 사용한다