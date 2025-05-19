# normal mapping

텍스쳐는 단순한 그림일 뿐 실제 질감, 요철을 반영하지 않음

질감을 반영하는 쉬운 방법으로는 실제로 모델링은 하는 방법이 있지만 단순한 면 하나에도 수많은 정점들이 생기게 되어 계산이 방대해짐

각 fragment 마다 normal 을 사용해 질감 표현에 사용

해당 normal 은 색과 같은 vec3 로 표현될 수 있으며 normal 을 이미지에 적용시켜 texture 로서 사용

## normal map 의 생성

normal 의 범위는 -1 ~ 1 이므로 이미지로 저장할때는 rgb 값과 대응되도록 0 ~ 1 사이로 정규화

```cpp
vec3 rgb_normal = normal * 0.5 + 0.5;
```

## normal map 의 사용

실제 사용시에는 정규화 된 rgb 값을 다시 normal 값으로 변환

```cpp
vec3 normal = texture(normalMap, fs_in.TexCoords).rgb;
normal = vec3(texture(normal_map, texcoord) * 2.0 - 1);
```

## tangent space

- 접선 공간, 표면 공간
- object 의 한 vertex 의 normal 을 수직축으로 하는 공간
- 즉 해당 면을 x, y 에 두고 면의 법선을 z 축으로 가지는 공간을 뜻함

현재는 face 의 방향과 normal map 의 방향이 일치하기 때문에 정상적인 normal mapping 이 가능

face 의 방향이 달라지면 이에 맞춰서 normal map 의 방향도 일치를 시켜야 함

이를 위해 법선을 항상 z 축으로 가지는 접선 공간에서 표현

![Screen Shot 2023-10-12 at 1.20.17 PM.png](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Screen_Shot_2023-10-12_at_1.20.17_PM.png)

TBN 행렬

- tangent
- bitangent
- normal

각각의 벡터를 나타냄

![Screen Shot 2023-10-12 at 2.02.56 PM.png](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Screen_Shot_2023-10-12_at_2.02.56_PM.png)

![Untitled](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Untitled.png)

![Untitled](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Untitled%201.png)

![Untitled](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Untitled%202.png)

![Untitled](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Untitled%203.png)

![Untitled](normal%20mapping%20413a37ded27b4c21ab3d6a5629ec6828/Untitled%204.png)

평면위의 세 점을 꼭지점으로 하는 삼각형을 사용해 T, B 벡터를 구하는 식의 풀이

### ex)

```cpp
// positions
glm::vec3 pos1(-1.0,  1.0, 0.0);
glm::vec3 pos2(-1.0, -1.0, 0.0);
glm::vec3 pos3( 1.0, -1.0, 0.0);
glm::vec3 pos4( 1.0,  1.0, 0.0);
// texture coordinates
glm::vec2 uv1(0.0, 1.0);
glm::vec2 uv2(0.0, 0.0);
glm::vec2 uv3(1.0, 0.0);
glm::vec2 uv4(1.0, 1.0);
// normal vector
glm::vec3 nm(0.0, 0.0, 1.0);
```

```cpp
glm::vec3 edge1 = pos2 - pos1;
glm::vec3 edge2 = pos3 - pos1;
glm::vec2 deltaUV1 = uv2 - uv1;
glm::vec2 deltaUV2 = uv3 - uv1;
```

- 면 위의 세 점을 사용해 삼각형의 가장자리 벡터와 uv 좌표 delta 를 구함

```cpp
float f = 1.0f / (deltaUV1.x * deltaUV2.y - deltaUV2.x * deltaUV1.y);

tangent1.x = f * (deltaUV2.y * edge1.x - deltaUV1.y * edge2.x);
tangent1.y = f * (deltaUV2.y * edge1.y - deltaUV1.y * edge2.y);
tangent1.z = f * (deltaUV2.y * edge1.z - deltaUV1.y * edge2.z);
tangent1 = glm::normalize(tangent1);

bitangent1.x = f * (-deltaUV2.x * edge1.x + deltaUV1.x * edge2.x);
bitangent1.y = f * (-deltaUV2.x * edge1.y + deltaUV1.x * edge2.y);
bitangent1.z = f * (-deltaUV2.x * edge1.z + deltaUV1.x * edge2.z);
bitangent1 = glm::normalize(bitangent1);
```

- 위의 식을 사용해 tangent, bitangent 벡터를 구함

```cpp
# vertex shader

#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;
layout (location = 3) in vec3 aTangent;
layout (location = 4) in vec3 aBitangent;

out VS_OUT {
    vec3 FragPos;
    vec2 TexCoords;
    vec3 TangentLightPos;
    vec3 TangentViewPos;
    vec3 TangentFragPos;
} vs_out;

uniform mat4 projection;
uniform mat4 view;
uniform mat4 model;

uniform vec3 lightPos;
uniform vec3 viewPos;

void main()
{
    vs_out.FragPos = vec3(model * vec4(aPos, 1.0));   
    vs_out.TexCoords = aTexCoords;
    
    mat3 normalMatrix = transpose(inverse(mat3(model)));
    vec3 T = normalize(normalMatrix * aTangent);
    vec3 N = normalize(normalMatrix * aNormal);
    T = normalize(T - dot(T, N) * N);
    vec3 B = cross(N, T);
    
    mat3 TBN = transpose(mat3(T, B, N));    
    vs_out.TangentLightPos = TBN * lightPos;
    vs_out.TangentViewPos  = TBN * viewPos;
    vs_out.TangentFragPos  = TBN * vs_out.FragPos;
        
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

- tangent, bitangent, normal 을 사용해 TBN 행렬을 만든 뒤 lightPos, viewPos, fragPos 에 곱하여 tangent space 에서의 lightPos, viewPos, fragPos 로 변환한 값을 fragment shader 로 전달
- fragment shader 에서는 해당 값들을 기존의 lightPos, viewPos, fragPos 대신 사용하여 계산

## assimp

assimp 를 사용하면 위 과정없이 계산 된 값을 사용가능

## nomalize normal

평균화 된 normal 을 사용하면 TBN 벡터가 수직이 아니게 되어 TBN 행렬이 직각이 아닐 수 있게됨

```cpp
vec3 T = normalize(normalMatrix * aTangent);
vec3 N = normalize(normalMatrix * aNormal);
T = normalize(T - dot(T, N) * N);
vec3 B = cross(N, T);
```

그램-슈미트 과정을 사용해 TBN 벡터를 다시 직교화 하여 사용