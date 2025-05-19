# 연관 컨테이너 associate container

**연관 컨테이너**

- key, value 처럼 연관 된 값을 하나의 쌍으로 저장하는 컨테이너
- 요소들에 대한 빠른 접근 가능
- 삽입되는 위치는 지정 불가
- balanced binary tree 나 hash table 을 이용해 구현되어있음

**종류**

- set
- multiset
- map
- multimap

**set, mulitset**

- set 헤더에 정의됨
- 데이터 자체를 키로써 사용
- 오름차순정렬, 검색속도가 빠르다
- set은 키의 중복을 허용하지 않는다
- multiset은 키의 중복을 허용한다
- 선언
    - set <템플릿인수> 객체이름;
- 함수
    - insert() : 컨테이너에 해당 데이터를 추가
    - erase() : 전달된 값과 같은 값을 가진 데이터를 전부 삭제

**map, multimap**

- map 헤더에 정의됨
- key, value 값을 가짐
- map 은 값의 중복을 허용하지 않는다, 즉 하나의 키에 하나의 값이 들어간다
- multimap 은 값의 중복을 허용한다, 즉 하나의 키에 여러개의 값이 들어갈 수 있다
- 선언
    - map<키인수, 값인수> 객체이름;
        - ex) map<string, int> mp;
- 함수
    - insert() : 키, 값쌍을 가진 요소를 추가한다
        - ex) mp.insert(pair<string, int>(“국어”, 80));
            - 익명의 객체 pair을 생성하여 추가한다
        - mp[“수학”] = 100;
            - 첨자 연산자를 이용해 추가한다
- ::iterator 반복자를 사용해 참조 시 it->first, it->second 처럼 key 는 first에, value 는 second 에 저장된다
- 각각 key, value 는 first, second 라는 멤버 변수에 저장되어있다

**순서가 지정되지 않은 컨테이너**

- unordered associate container 는 기존의 연관 컨테이너와 동일하게 동작한다
- 기존에는 tree 구조를 이용해 동작하며 unordered 연관 컨테이너는 hash table 기반으로 작동한다
- 추가, 삭제속도의 증가, 검색알고리즘 사용가능
- ordered 에서는 양방향 반복자를 지원
- unordered 에서는 순방향 반복자만 지원
- C++11 부터 사용 가능
    - unordered_set
    - unordered_multiset
    - unordered_map
    - unordered_multimap