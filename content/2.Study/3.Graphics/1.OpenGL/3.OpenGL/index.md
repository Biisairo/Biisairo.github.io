# OpenGL

## 1. OpenGL 이란?

### 1. OpenGL

Khronos 그룹에서 만든 그래픽, 이미지 라이브러리에 대한 명세

각각의 함수의 결과, 반환값이 설명 되어 있음

각각의 함수에 대한 구현방식은 해당 함수를 구현하는 개발자의 몫이며 구현의 결과가 같다면 함수의 내부구현은 다를 수 있음

함수를 구현하는 해당 개발자는 그래픽 카드 제조업체의 개발자이며 각 제조업체들은 본인들의 그래픽카드에 맞는 함수를 구현하게 됨

제조사에서 드라이버를 배포, 설치할때 opengl 함수의 구현도 같이 설치가 되게 됨

### 2. core-profile vs immediate mode

과거의 방식은 immediate mode(인스턴스모드, 고정 함수 파이프라인)

대부분의 기능이 라이브러리 내부에 숨겨져 있으며 개발자가 opengl 의 개산 방식을 컨트롤 할 수 없었음

3.2 버전 이후 core-profile mode 를 사용

core-profile mode 를 사용하면 구 버전의 함수를 사용할 때 opengl 에서 오류를 발생시킴 - 최신의 함수 사용을 강제함

### 3. state machine

opengl 은 하나의 state machine : opengl 의 현재 작동을 정의하는 변수들의 집합

현재 opengl 의 상태를 opengl context 라고 명명

context 의 옵션을 설정하고 상태를 변경하고 버퍼를 조작하여 랜더링을 함

ex)

그리는 방식을 변경할 때 그리는 방식을 설정하는 변수를 변경하게 됨

### 4. object

opengl 의 구조중 많은 부분이 다른 언어와의 호환성을 염두에 두고 객체로 추상화가 되어있음

예를 들어 glGenObject 로 객체를 생성한 뒤 GL_WINDOW_TARGET 에 binding 해 주면 해당 객체를 window 의 크기 등을 set 할 수 있는 객체로서 작동하게 됨

## 2. OpenGL Library 들

### 1. OpenGL 의 window 를 만들고, event 를 처리하고, context 를 관리해주는 라이브러리

GLUT, freeGLUT

- GLUT 가 개발중단됨에 따라 open source 로 freeGLUT 가 개발중
- 창 관리, 이벤트처리, 기본적인 입력 처리
- 오래된 라이브러리로 레거시 코드와의 호환성을 염두에 두고 사용

GLFW

- C 로 만들어진 라이브러리
- 창 관리, 입력 처리, OpenGL 컨텍스트 성생 등의 그래픽 작업 담당
- 가벼운 라이브러리로 복잡한 기능을 원하면 다른 라이브러리와 함께 사용해야 함

SDL

- C 를 기반으로 만들어진 라이브러리
- 그래픽 작업 뿐 아니라 오디로, 창 관리, 네트워크 등의 멀티미디어 작업을 해 줌
- 게임 개발 시 많이 쓰임

SFML

- SDL 과 비슷하나 C++ 를 사용해 객체지향 프로그래밍 스타일을 지원

### 2. 링킹을 위한 라이브러리

아래 라이브러리들은 opengl 를 링킹 해 주는 역할을 함

opengl 명세에 적혀있는 함수와 실제 구현된 함수를 연결해 주는 역할을 함

사용할 때에는 opengl context 를 생성한 후 초기화 해주어야 함

GLEW

- opengl 확장 라이브러리
- 최신 기능의 확장에 유용
- 레거시에 많이 사용

GLAD

- opengl 의 확장함수를 로드하는 라이브러리

### 3. 기타 라이브러리

GLM

- 벡터, 행렬 연산을 위한 라이브러리
- 3D 그래픽 개발 시 유용

Assimp

- 3D 모델 포멧을 로드하는 라이브러리

glTF

- 3D 모델, 장면의 전송을 위한 포맷

## 3. 작동 과정

## 1. opengl context 초기화

- glfw, sdl 등의 라이브러리를 사용해 context 를 초기화 해줌

## 2. opengl 초기화

- glad, glew 를 사용해 함수포인터를 링킹 해 줌

## 3. shader 작성

- shader 는 그래픽 카드에서 실행되는 프로그램
- 각각의 좌표를 계산하고 색을 계산하여 실제 출력될 정점의 위치와 색을 반환함

## 4. opengl 상태 설정

- opengl 의 상태를 설정하여 rendering pipeline 을 컨트롤

## 5. 데이터 로딩

- 객체의 데이터를 cpu 에서 gpu 로 이동
- VBO, VAO 등의 데이터 객체를 사용

## 6. rendering loop

- 루프를 만들에 루프 내에서 그리기를 수행

## 7. rendering

- render 작업을 수행
- 각 render 에는 render pipeline 이 수행되며 shader 가 실행 됨

## 8. event process

- 사용자의 입력을 처리

## 9. 화면 업데이트

- window 에는 두개의 buffer 가 있으며 각각 back buffer, front buffer 라고 불림
- front buffer 는 현재 출력이 되고있는 buffer
- back buffer 는 rendering 되고있는 buffer
- 매 loop 마다 swapbuffer 를 하여 back, front buffer 를 바꾸어줌

## 10. end

- 프로그램이 끝날 때 opengl 의 자원을 정리하는 과정
- texture, object, shader 등을 해제

## 4. Rendering pipeline

![Untitled](OpenGL%20bb3a8613e80843b68e646ec6a1f840ac/Untitled.png)

vertex specification

- 정점 속성의 나열, 파이프 라인을 통해 전달되어 짐

vertex shader

- 정점의 최종위치 계산

tasselation

- 선택, 더 작은 삼각형 매쉬로 나누어짐

geometry shader

- 선택, 하나의 프리미티브를 사용해 0개 이상의 출력 프리미티브를 생성, 기본요소를 제거하거나 더 잘게 나눌 수 있음

vertex post-processing

- 가장 중요한 부분은 clipping 으로 보는 범위 외부의 요소를 삭제

primitive assembly

- 각 정점을간단한 프리미티브(점, 선, 삼각형)로 정렬

rasterization

- 프리미디브의 레스터화, 출력은 fragment

fragment shader

- 필수는 아님, 해당 조각의 색을 결정

per-sample operations

- 샘플별로 사용자가 설정한 작업들을 실행
- vertex shader
    - VAO 에 정의된 vertex array 를 사용해 정점을 내려받아 정점의 위치를 계산
    - 하나의 정점의 한번의 shader 가 실행됨
- tessellation shader
    - vertex shader 의 optional shader
    - tessellation control shader 와 tessellation evaluation shader 의 두단계로 세분화 됨
    - 정점 데이터를 더 작은 값으로 세분화하여 계산
    - 곡면의 표현 등 더 세분화 된 작업을 할 때 사용
    - tessellation control shaser (TCS)
        - vertex shader 로 부터 input 을 받아 수행
        - tessellation engine 으로 보낼 tessellation level 을 결정해 TES 로 보낼 데이터를 생성
        - 입력된 patch 를 분할해 tessellation 인자를 생성
    - tessellation evaluation shader (TES)
        - TCS 로부터 tessellation 데이터를 받아 각각의 position 을 계산
- geometry shader
    - vertex shader 의 optional shader
    - pipe line 내의 data flow 양을 조절
    - 하나의 primitive 를 사용해 여러 이미지를 생성할 때 사용
    - GPU 의 병렬수행을 컨트롤
- primitive assembly
    - vertex 들을 line, triangle 등으로 그룹화한 primitive 생성
- clipping, world space transformation
    - window 에 보이는 영역을 제외한 부분을 잘라내는 과정
    - 그려질 좌표를 계산하고 실제 보이는 영역 외의 부분을 잘라냄
- viewport transformation
    - 정규화된 좌표를 화면에 보일 물리적인 좌표계로 변환하는 과정
- face culling
    - 점, 선이 아닌 삼각형의 경우 삼각형의 위치, 방향에 따라 그려지지 않을 수 있음
    - 가려져있는 삼각형같은경우 랜더링 될 필요가 없기 때문에 face culling 단계에서 랜더링 되지 않도록 함
    - 점, 선의 경우 이 과정을 거치지 않음
- rasterization
    - 생성된 primitive 들을 pixel 화 하는 단계
    - 계산되지 않은 pixel 에 대해서는 정점들을 사용해 보간
- pixel shader, fragment shader
    - programming 이 가능한 shader
    - 각 fragment(pixel) 의 색을 결정하는 단계
- per-sample processing
    - opengl pipeline 의 마지막
    - 랜더링의 최종 결과가 기록됨
    - pixel operation
        - pixel ownership test
            - 랜더링 되어지는 window 가 다른 창에 가려지는 경우 가려진 부분은 opengl 이 관리하지 않는 pixel 이 됨
            - 다른 창에 가려지는 pixel 은 해당 테스트에 실패하여 버려지게 됨
        - sissor test
            - 사용할 경우 사용자가 지정한 영역 이외의 부분이 출력되지 않도록 함
        - stencil test
            - stencil buffer 가 존재해야 함
            - sencil buffer 와 만들어진 fragment 를 비교
        - depth test
            - fragment shader 전, 후에 실행
            - 각각 fragment 에 쓰여진 depth 를 비교
            - depth 버퍼를 사용하며 각 fragment 의 depth가 기록되어짐
            - fragment 후에 실행되는 것이 일반적이나 이전에 실행되어지게 할 수 있으며 이렇게 하면 fragment shader 의 실행을 줄일 수 있어 성능절약이 가능