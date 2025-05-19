# Skeleton hierarchy

![Untitled](Skeleton%20hierarchy%20249b6f8adea941c7ad8e75a05e4fee59/Untitled.png)

위 그림은 skeleton hierarchy 중 하나

위와 같이 root 인 pevis 를 시작으로 가지가 뻗어 나가며 skeleton 형상이 생기게 됨

LT (local transform)

- 해당 노드가 가진 회전값 * 이동값

GT (global transform)

- 내 LT * 부모의 GT

자식은 부모의 transform 을 모두 곱한 값으로 나오게 된다