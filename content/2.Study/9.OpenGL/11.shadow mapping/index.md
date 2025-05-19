# shadow mapping

빛의 관점에서 장면을 랜더링 하여 빛의 관점에서 볼 수 없는 모든 부분이 그림자

ray tracing 같은 경우 광원에서 출발하는 광선을 추적해 빛이 닿는부분과 닿지 않는 부분을 계산

실시간 랜더링에서는 효율적이지 않은 방법

## depth buffer 를 사용

빛의 관점에서 장면을 랜더링 한 다음 depth buffer 를 텍스쳐로 저장

광원을 기준으로 가장 가까운 깊이값을 샘플링할 수 있음

해당하는 모든 깊이값을 depth map, 혹은 shadow map 이라고 하는 texture 로 저장

광원에 특정한 view, projection 행렬을 사용해 장면을 랜더링

1. 랜더링 하고자 하는 점을 빛의 좌표공간으로 변환
2. 공간을 랜더링해 해당 점의 깊이를 구함
3. 빛의 시점에서 해당점에서의 실제 보이는 깊이값을 구함
4. 두 값을 비교해 해당 점이 그림자져있는지 아닌지 판단

## depth map

```cpp
unsigned int depthMapFBO;
glGenFramebuffers(1, &depthMapFBO);
```

장면을 랜더링 하기 위한 framebuffer 를 생성

- GL_CLAMP_TO_EDGE 나 GL_CLAMP_TO_BORDER 를 사용해 랜더링 되는 장면 밖을 처리

```cpp
const unsigned int SHADOW_WIDTH = 1024, SHADOW_HEIGHT = 1024;

unsigned int depthMap;
glGenTextures(1, &depthMap);
glBindTexture(GL_TEXTURE_2D, depthMap);
glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH_COMPONENT, 
             SHADOW_WIDTH, SHADOW_HEIGHT, 0, GL_DEPTH_COMPONENT, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
// glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE); 
// glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_BORDER);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_BORDER);
float borderColor[] = { 1.0f, 1.0f, 1.0f, 1.0f };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```

프래임 버퍼의 depth buffer 로 사용할 2D texture 를 생성

- 깊이 값만 사용하면 되기 때문에 format 을 GL_DEPTH_COMPONENT 로 설정
- width, height 는 해당 map 의 해상도가 됨

```cpp
glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_TEXTURE_2D, depthMap, 0);
glDrawBuffer(GL_NONE);
glReadBuffer(GL_NONE);
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

depth texture 를 framebuffer 에 연결

- color buffer 가 없으면 제대로 된 framebuffer 를 생성할 수 없음
- opengl 에 이를 명시적으로 말하기 위해 draw, read 를 할 필요가 없다는 뜻의 GL_NONE 으로 설정

```cpp
// 1. first render to depth map
glViewport(0, 0, SHADOW_WIDTH, SHADOW_HEIGHT);
glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
	glClear(GL_DEPTH_BUFFER_BIT);
	ConfigureShaderAndMatrices();
	RenderScene();
glBindFramebuffer(GL_FRAMEBUFFER, 0);
// 2. then render scene as normal with shadow mapping (using depth map)
glViewport(0, 0, SCR_WIDTH, SCR_HEIGHT);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
ConfigureShaderAndMatrices();
glBindTexture(GL_TEXTURE_2D, depthMap);
RenderScene();
```

## light space transform

camera 의 위치가 아닌 light 의 위치에서 투영을 해야 하기 때문에 이에 맞는 projection, view 행렬을 사용

위의 ConfigureShaderAndMatrices 에서 이뤄질 부분

빛의 종류에 따라 투영 행렬의 종류가 결정 됨

- directional light 에서는 빛이 평행하게 한 방향에서 들어오기 때문에 projection 행렬을 ortho(직교)로 만들어야 함

```cpp
// ex)
float near_plane = 1.0f, far_plane = 7.5f;
glm::mat4 lightProjection = glm::ortho(-10.0f, 10.0f, -10.0f, 10.0f, near_plane, far_plane);
```

- 빛의 위치에서 투영을 하기 위한 view 행렬도 새로 만들어 줌

```cpp
// ex)
glm::mat4 lightView = glm::lookAt(glm::vec3(-2.0f, 4.0f, -1.0f), 
                                  glm::vec3( 0.0f, 0.0f,  0.0f), 
                                  glm::vec3( 0.0f, 1.0f,  0.0f));
// 각각 position, center, up
// 위 예시는 (-2, 4, -1) 위치에서 원점 방향을 보는 빛
```

### render to depth map

빛의 관점에서 render 하기 위한 shader

```cpp
// vertex shader
uniform mat4 lightSpaceMatrix;
uniform mat4 model;

void main()
{
    gl_Position = lightSpaceMatrix * model * vec4(aPos, 1.0);
}
// lightSpaceMatrix = lightProjection * lightView
```

```cpp
// fragment shader
void main()
{             
    // gl_FragDepth = gl_FragCoord.z;
}
```

fragment shader 는 실제 출력할 것이 없으므로 비어있어도 됨

```cpp
simpleDepthShader.use();
glUniformMatrix4fv(lightSpaceMatrixLocation, 1, GL_FALSE, glm::value_ptr(lightSpaceMatrix));

glViewport(0, 0, SHADOW_WIDTH, SHADOW_HEIGHT);
glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
    glClear(GL_DEPTH_BUFFER_BIT);
    RenderScene(simpleDepthShader);
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

위 과정을 통해 빛이 보는 관점에서의 depth map 을 랜더링 함

```cpp
#version 400 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D depthMap;

void main()
{             
    float depthValue = texture(depthMap, TexCoords).r;
    FragColor = vec4(vec3(depthValue), 1.0);
}
```

위의 결과로 만들어진 depth map 을 출력해 보여주는 fragment shader

## render shadow

이후 object 를 render 할 때 light projection, light view 를 shader 에 전달

vertex shader 에서 fragment shader 로 projection , view 대신 light projection, light view 를 곱한 light fragment coordinate 를 추가로 전달

- 위 좌표가 shadow map 에서 depth 를 구할 때 사용됨

```cpp
float calcShadow(vec4 fragPosLightSpace) {
	float bias = 0.005;
	vec3 projCoords = fragPosLightSpace.xyz / fragPosLightSpace.w;
	projCoords = projCoords * 0.5 + 0.5;
	float closestDepth = texture(shadowMap, projCoords.xy).r;
	float currentDepth = projCoords.z;
	float shadow = currentDepth - bias > closestDepth  ? 1.0 : 0.0;
	if(projCoords.z > 1.0)
		shadow = 0.0;
	return shadow;
}
```

fragment shader 에서 현재 정점의 light 관점에서의 좌표인 fragPosLightSpace 를 사용해 shadow map 에서의 해당 점의 depth와 현재 depth를 비교해 해당 light 가 닿는 부분인지 아닌지를 판단

```cpp
color = vec4(COLOR * (light * (1.0 - shadow) + ambient), 1);
```

이후 해당 빛을 계산할때 적용

## moire (모아레) 현상

위 과정을 끝낸 후 랜더링 된 이미지는 아래와 같은 패턴이 생김

![Untitled](shadow%20mapping%20a98e28d25a1b4b5da034a419cd0d6e67/Untitled.png)

이 패턴은 light 의 관점에서 기울어진 면을 그릴때 나오는 현상

![Screen Shot 2023-10-06 at 2.21.22 PM.png](shadow%20mapping%20a98e28d25a1b4b5da034a419cd0d6e67/Screen_Shot_2023-10-06_at_2.21.22_PM.png)

실제로 샘플링을 하면 각각의 단위 texel 로 샘플링이 되며 이는 기울어진 면에서 계단 모양이 됨

이때 샘플링 된 값과 실제 면에서 오는 차이때문에 줄이 그어지게 갬

![Screen Shot 2023-10-06 at 2.22.30 PM.png](shadow%20mapping%20a98e28d25a1b4b5da034a419cd0d6e67/Screen_Shot_2023-10-06_at_2.22.30_PM.png)

depth 를 비교할 때 아주 작은 수치의 bias 를 주어 해당 현상을 해결

```cpp
float bias = 0.005;
//
float bias = max(0.05 * (1.0 - dot(normal, lightDir)), 0.005);

float shadow = currentDepth - bias > closestDepth  ? 1.0 : 0.0;
```

아래 식을 사용하면 normal 과 빛의 방향을 기준삼아 0.05 ~ 0.005 의 편향값을 사용할 수 있음

## peter panning

피터팬에서 따온 단어로 그림자가 물체와 떨어져있어 물체가 날아다니는 것 처럼 보이는 현상

![Screen Shot 2023-10-06 at 2.54.39 PM.png](shadow%20mapping%20a98e28d25a1b4b5da034a419cd0d6e67/Screen_Shot_2023-10-06_at_2.54.39_PM.png)

shadow bias 를 크게 주면 생김

bias 값을 정밀하게 계산하는 것이 한가지 해결법

해당 문서에는 face cull 을 사용해 front-face cull 을 해 뒷면만을 사용하여 그림자를 그리는 것을 해결책으로 제시

## PCF

그림자의 가장자리에는 계단현상이 생김

depth map 의 해상도를 높이는 방식으로 해결가능

### PCF percentage closer filtering

위에 언급한 그림자의 계단 현상을 해결하는 방법

depth map 을 샘플링 할 때 해당 texel 만 사용하는 것이 아닌 주변의 texel 들을 가져와 평균화해 사용하는 것

```cpp
float shadow = 0.0;
vec2 texelSize = 1.0 / textureSize(shadowMap, 0);
for(int x = -1; x <= 1; ++x)
{
    for(int y = -1; y <= 1; ++y)
    {
        float pcfDepth = texture(shadowMap, projCoords.xy + vec2(x, y) * texelSize).r; 
        shadow += currentDepth - bias > pcfDepth ? 1.0 : 0.0;        
    }    
}
shadow /= 9.0;
```

texel 의 size 를 textureSize 함수를 사용해 구한다음 상하좌우 8방향의 그림자 여부를 더해 9로 나눠 평균화

## perspective projection

광원을 directional light 가 아닌 일반 조명처럼 보이게 하려면 원근 투영을 사용하면 됨

빛의 projection 을 ortho 대신 perspective 를 사용

이외에는 동일

디버깅을 위해 depth map 을 보려고 할 때 ortho 와 같은 shader 를 사용하면 하얗게만 보일 수 있음

```cpp
#version 400 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D depthMap;
uniform float near_plane;
uniform float far_plane;

float LinearizeDepth(float depth)
{
    float z = depth * 2.0 - 1.0; // Back to NDC 
    return (2.0 * near_plane * far_plane) / (far_plane + near_plane - z * (far_plane - near_plane));
}

void main()
{             
    float depthValue = texture(depthMap, TexCoords).r;
    FragColor = vec4(vec3(LinearizeDepth(depthValue) / far_plane), 1.0); // perspective
    // FragColor = vec4(vec3(depthValue), 1.0); // orthographic
}
```

ortho 를 사용하면 선형으로 값이 나와 문제가 되지 않으니 projection 을 사용하면 비선형의 값이 나와 거의 하얗게만 보임

위 과정을 통해 비선형으로 구해진 depth 값을 선형으로 변환

디버깅 시에만 사용