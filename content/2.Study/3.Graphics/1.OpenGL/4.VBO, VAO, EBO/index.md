# VBO, VAO, EBO

## VBO

- Vertex Buffer Object
- 정점들을 모아놓은 객체
- GPU 와 CPU 사이에 데이터 전송이 많으면 속도가 느려지기 때문에 한번에 많은 양의 데이터를 넘겨주는 것이 좋음
- 이때 정점 데이터를 한번에 보내기 위한 객체

```cpp
glGenBuffers(GLsizei size, GLuint &buffers)
```

- 각각의 buffer 는 고유한 ID 를 가짐
    - size 갯수 만큼의 buffer 를 생성하고 해당 buffer 의 id 를 buffers 에 저장

```cpp
glBindBuffer(GLenum target, GLuint buffer)
```

- 여러가지 종류의 buffer 가 존재하며 생성한 buffer 에 필요한 타입의 buffer 를 bind 함
    - 만들기를 원하는 타입을 target 에 전달
    - bind 하기를 원하는 buffer 의 id 를 buffer 에 전달
    - 이후 나오는 glBufferData, glVertexAttribPoint 와 같은 함수는 해당 시점 bind 되어있는 buffer 를 기준으로 작동
    
    ```cpp
    glBufferData(GLenum target,
    							GLsizeiptr size,
    							const void * data,
    							GLenum usage)
    ```
    
- 생성된 buffer 에 데이터를 넣는 과정이 필요
    - target 에 buffer type
    - size 에 총 데이터의 크기
    - data 에 데이터의 시작 주소값
    - usage 에 해당 데이터의 사용 목적

```cpp
glVertexAttribPointer(GLuint index,
											GLint size,
											GLenum type,
											GLboolean normalized,
											GLsizei stride
											const void * pointer)
```

- opengl 이 현재 bind 된 buffer 를 다루는 방법에 대해서 기입
    - index 에 설정할 index 번호 (shader 의 layout 에 들어가는 location 값)
    - size 는 각 인자의 요소 갯수 (vec3 인 경우 3)
    - type 에는 요소의 타입
    - nomalized 에는 정규화 여부
    - stride 에는 각 인자의 크기 (vec3 인 경우 3 * sizeof(float))
    - pointer 에는 각 인자의 시작 지점으로부터의 오프셋
    
    ```cpp
    float vertices[] = {
        // positions         // colors
         0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,   // bottom right
        -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,   // bottom left
         0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f    // top 
    };
    ```
    
    ![Untitled](VBO,%20VAO,%20EBO%202fafa6b59052464a822dddf01743b802/Untitled.png)
    
    - 위 예시에서 array 에 pos 와 color 가 동시에 존재
    - 각각의 vertex 의 pos 와 color 를 동시에 알려주려면 stride 와 pointer offset 을 설정해 주어야 함
    
    ```cpp
    // pos
    glVertexAttribPointer(0,
    											3,
    											GL_FLOAT,
    											GL_FALSE,
    											6 * sizeof(float),
    											(void*)0);
    // color
    glVertexAttribPointer(1,
    											3,
    											GL_FLOAT,
    											GL_FALSE,
    											6 * sizeof(float),
    											(void*)(3* sizeof(float)));
    ```
    
    ## EBO
    
- VBO 와 같은 버퍼 객체
- 정점을 그리는 순서를 결정하는 인덱스를 저장
- indexed drawing 방식
- 삼각형 이상의 face 를 그릴때 모든 정점을 삼각형으로 표현하면 배열에 중복되는 정점이 생겨 overhead 가 발생함
- 이를 막기 위한 방식
    
    
    ## VAO
    
- vertex array object
- VBO 를 포함해 vertex attribute call 들이 저장되어있음