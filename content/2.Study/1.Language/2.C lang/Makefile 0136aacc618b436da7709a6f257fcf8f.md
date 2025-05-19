# Makefile

**파일이 빌드되는 과정**

foo.c (source file)

compile (gcc -c) -> foo.o (object file)

linking -> a.out (execute file)

**make 구조**

target : prerequisites

(tab)recipe

target - 해당 recipe 로 만들어지는 최종 파일

prerequisites - make 를 하는데 사용되어지는 파일들, target 을 만드는데 필요한 파일의 목록

recipes - target 을 만드는데 사용되는 명령어의 목록, 탭으로 구분

**변수**

CC = gcc

라고 파일 내에서 변수선언을 한 뒤

이후에 $(CC) 처럼 $() 로 불러와 사용 가능

이때 $(CC) 는 gcc 로 대체되어 명령어가 실행 됨

**.PHONY:**

clean, fclean, re 와 같은 매크로와 같은 이름의 파일이 있을 때

make 는 해당 파일이 이미 존재하는 것으로 판단해 작동하지 않을 수 있음

.PHONY: clean fclean re

처럼 .PHONY: 뒤에 해당 명령을 넣어주면 파일의 유무와 관계없이 명령을 실행

**패턴**

자동변수 - recipe 에 들어감

- $@ : target 에 대응
- $< : 의존파일 목록에서 첫번째 파일에 대응
- $^ : 의존파일 전체 목록에 대응
- $? : 타겟보다 최신의 의존파일들에 대응

**함수**

$(OBJS:.o=.d)

- OBJS 변수 내에서 .o 인 부분은 .d 로 대치 하라는 함수

$(wildcard 조건)

- 조건에 맞는 파일들을 뽑아내 주는 함수
- ex) $(wildcard srcs/*.c) 를 하면 srcs 디랙토리 안에 있는 .c 파일들을 뽑아낸다

$(notdir 경로)

- 경로에 들어간 변수에서 파일명만 남기고 경로를 지워주는 함수

$(patsubst 패턴, 치환 후, 변수)

- 변수 내의 패턴을 찾은 후 해당 패턴을 치환 후 문자로 치환
- ex) $(patsubst %.o, obj/%.o, $(변수)) 를 하면 변수내의 .o 파일들이 obj/*.o 로 변환된다

$(filter 찾을문자, 대상문자)

- 대상문자내의 찾을문자를 가져옴
- 찾을 문자가 존재하지 않을 시 빈 스트링을 가져온다

$(if 조건, 참일시[, 거짓일시])

- 조건이 참일시 참을 조건이 거짓일시 거짓이 대응됨
- 거짓일 시 변수는 적지 않아도 됨

**= := +=**

- =
    - 오른쪽의 값을 왼쪽에 대입
    - 최종 컴파일 과정에서의 값이 대입된다
        - A = t
        - B = ${A}
        - A = r
        - 이 경우 B = r 이 된다
- :=
    - 오른쪽의 값을 왼쪽에 대입
    - 대입 시점의 값을 넣는다
        - A = t
        - B := ${A}
        - A = r
        - 이 경우 B = t 가 된다
- +=
    - 오른쪽의 값을 왼쪽에 추가
        - A = t
        - B = r
        - B += ${A}
        - 이 경우 B = r t 가 된다

**MAKECMDGOALS**

target 하는 파일들의 이름들의 리스트