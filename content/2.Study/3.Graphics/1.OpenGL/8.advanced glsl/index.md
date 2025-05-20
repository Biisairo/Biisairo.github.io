# advanced glsl

## vertex shader 변수

### gl_Position

vertex shader 의 출력 벡터

정점의 좌표를 나타냄

### gl_PointSize

glPointSize 를 사용해 정점의 크기를 정하는 것과 같은 효과

출력할 점의 넓이와 높이를 정할 수 있음

glEnable 을 사용해 GL_PROGRAM_POINT_SIZE 를 활성화 시켜주어야 함

gl_Position 처럼 변수에 값을 할당시켜 줌

### gl_VertexID

gl_Position, gl_PointSize 는 출력변수

gl_VertexID 는 입력 변수, 즉 읽어올 수 있는 변수

현재의 정점 ID 를 불러옴

glDrawElements 를 사용할 때 현재 인덱스 번호를 불러옴

glDrawArrays 를 사용할 때 랜더 이후 호출된, 처리된 정점의 번호를 가져옴

## fragment shader 변수

### gl_FragCoord

해당 조각의 화면에서의 좌표를 받아옴

### gl_FrontFacing

face culling 에서 확인된 해당 fragment 가 앞면인지 뒷면인지 확인

GL_FACE_CULL 을 enable 시켰을 때 사용 가능

앞면일 경우 true, 뒷면일 경우 false 로 정의되어있음

### gl_FragDepth

gl_FragCoord 는 깊이를 알 수 있지만 읽기전용 변수

gl_FragDepth 는 출력변수로 해당 fragment 의 깊이를 설정할 수 있음

0 ~ 1 사이의 값을 적용

따로 설정하지 않을 시 gl_FragCoord 의 z 값을 default 로 사용

depth test 에서는 gl_FragDepth 를 사용하면 값이 바뀔 수 있기 때문에 pre depth test 를 하지 않게됨

- 성능저하가 올 수 있음
- opengl 4.2 이후에서는 layout 을 사용해 이를 중재할 수있음
    - layout (depth_<conditon>) out float gl_FragDepth;
        
        ![Screen Shot 2023-09-25 at 4.34.11 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.34.11_PM.png)
        

## Interface block

vertex shader 에서 fragment shader 로 변수를 보낼때 in/out 선언을 사용

규모가 커지면 이를 그룹화 해서 사용할 수 있음

구조체 선언과 비슷하나 struct 키워드가 아닌 in/out 키워드를 사용

![Screen Shot 2023-09-25 at 4.35.47 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.35.47_PM.png)

![Screen Shot 2023-09-25 at 4.36.00 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.36.00_PM.png)

![Screen Shot 2023-09-25 at 4.35.52 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.35.52_PM.png)

![Screen Shot 2023-09-25 at 4.36.07 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.36.07_PM.png)

## Uniform buffer objects

쉐이더를 여러개를 사용하면 쉐이더 마다 uniform 을 따로 설정해야 했음

uniform buffer 를 만들어 동일한 uniform 을 한번만 설정하여 넣어줄 수 있음

![Screen Shot 2023-09-25 at 4.38.07 PM.png](advanced%20glsl%207a205ef7cc254ab7ae7abb9822f3c4fb/Screen_Shot_2023-09-25_at_4.38.07_PM.png)

## Uniform block layout

위에서 uniform buffer 를 사용할 경우 예약된 메모리를 버퍼에 저장할 뿐 해당 메모리의 type 은 저장하지 않음

glsl 에서는 shared layout 은 균일한 메모리 레이아웃을 사용

메모리에 따라 offset 을 지정할 수 있지만 std140 이라고 하는 스텐다드를 사용해 이를 해결

위에서 사용한 std140 의 경우 N 으로 표기되는 4바이트의 블럭을 기준으로 사용

## Uniform buffers

```cpp
unsigned int uboExampleBlock;
glGenBuffers(1, &uboExampleBlock);
glBindBuffer(GL_UNIFORM_BUFFER, uboExampleBlock);
glBufferData(GL_UNIFORM_BUFFER, 152, NULL, GL_STATIC_DRAW); // allocate 150 bytes of memory
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

버퍼에 데이터를 넣을 때 해당 객체를 bind 하고 glBufferSubData 를 사용해 메모리를 업데이트

해당 버퍼를 사용하는 쉐이더는 버퍼안의 메모리를 사용하게 됨

```cpp
// code
unsigned int lights_index = glGetUniformBlockIndex(shaderA.ID, "Lights");   
glUniformBlockBinding(shaderA.ID, lights_index, 2); // 2 는 binding point
// glsl
layout(std140, binding = 2) uniform Lights { ... };
```

glGetUniformBlockIndex 를 사용해 사용할 uniform 의 index 를 가져와 필요한 만큼의 블록을 바인딩 함

```cpp
glBindBufferBase(GL_UNIFORM_BUFFER, 2, uboExampleBlock); 
// or
glBindBufferRange(GL_UNIFORM_BUFFER, 2, uboExampleBlock, 0, 152);
```

실제 버퍼에 바인딩

### example

```cpp
unsigned int uniformBlockIndexRed    = glGetUniformBlockIndex(shaderRed.ID, "Matrices");
unsigned int uniformBlockIndexGreen  = glGetUniformBlockIndex(shaderGreen.ID, "Matrices");
unsigned int uniformBlockIndexBlue   = glGetUniformBlockIndex(shaderBlue.ID, "Matrices");
unsigned int uniformBlockIndexYellow = glGetUniformBlockIndex(shaderYellow.ID, "Matrices");  
  
glUniformBlockBinding(shaderRed.ID,    uniformBlockIndexRed, 0);
glUniformBlockBinding(shaderGreen.ID,  uniformBlockIndexGreen, 0);
glUniformBlockBinding(shaderBlue.ID,   uniformBlockIndexBlue, 0);
glUniformBlockBinding(shaderYellow.ID, uniformBlockIndexYellow, 0);
```

- 각각의 쉐이더에 같은 바인딩 포인트에 block 을 바인딩

```cpp
unsigned int uboMatrices
glGenBuffers(1, &uboMatrices);
  
glBindBuffer(GL_UNIFORM_BUFFER, uboMatrices);
glBufferData(GL_UNIFORM_BUFFER, 2 * sizeof(glm::mat4), NULL, GL_STATIC_DRAW);
glBindBuffer(GL_UNIFORM_BUFFER, 0);
  
glBindBufferRange(GL_UNIFORM_BUFFER, 0, uboMatrices, 0, 2 * sizeof(glm::mat4));
```

- buffer 를 생성한뒤 해당 버퍼에 필요 범위 (mat4 * 2 의 사이즈) 를 buffer 에 할당

```cpp
glm::mat4 projection = glm::perspective(glm::radians(45.0f), (float)width/(float)height, 0.1f, 100.0f);
glBindBuffer(GL_UNIFORM_BUFFER, uboMatrices);
glBufferSubData(GL_UNIFORM_BUFFER, 0, sizeof(glm::mat4), glm::value_ptr(projection));
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

- projection matrix 를 offset 0 위치에 할당

```cpp
glm::mat4 view = camera.GetViewMatrix();	       
glBindBuffer(GL_UNIFORM_BUFFER, uboMatrices);
glBufferSubData(GL_UNIFORM_BUFFER, sizeof(glm::mat4), sizeof(glm::mat4), glm::value_ptr(view));
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

- view matrix 를 offset sizeof(glm::mat4) 위치에 할당

model matrix 는 다른 model 을 사용하므로 기존과 같게 shader 별로 uniform 전달

```cpp
#version 330 core
layout (location = 0) in vec3 aPos;

layout (std140) uniform Matrices
{
    mat4 projection;
    mat4 view;
};
uniform mat4 model;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

- shader code