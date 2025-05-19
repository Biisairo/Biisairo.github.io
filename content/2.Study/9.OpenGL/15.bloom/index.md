# bloom

광원을 중심으로 하는 빛번짐의 구현

- HDR 을 사용해 framebuffer 로 랜더링
- 특정 밝기를 초과하는 fragment 를 추출
- blur 와 같은 방식을 사용해 추출한 이미지를 흐리게 만듬
- 기존의 framebuffer 이미지와 흐리게 만든 이미지를 합성

![Untitled](bloom%204efc599c9d574b2cbf28856652abd981/Untitled.png)

## extracing bright color

같은 장면을 두번 랜더링 하면 필요없는 랜더링이 추가로 들어감

### MRT (multiple render targets)

HDR framebuffer 를 만들 때 두개의 colorBuffer 를 만들어 attach

```cpp
unsigned int hdrFBO;
glGenFramebuffers(1, &hdrFBO);
glBindFramebuffer(GL_FRAMEBUFFER, hdrFBO);
unsigned int colorBuffers[2];
glGenTextures(2, colorBuffers);
for (unsigned int i = 0; i < 2; i++)
{
    glBindTexture(GL_TEXTURE_2D, colorBuffers[i]);
    glTexImage2D(
        GL_TEXTURE_2D, 0, GL_RGB16F, SCR_WIDTH, SCR_HEIGHT, 0, GL_RGB, GL_FLOAT, NULL
    );
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    // attach texture to framebuffer
    glFramebufferTexture2D(
        GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0 + i, GL_TEXTURE_2D, colorBuffers[i], 0
    );
}

unsigned int attachments[2] = { GL_COLOR_ATTACHMENT0, GL_COLOR_ATTACHMENT1 };
glDrawBuffers(2, attachments);
```

이후 두번째 colorBuffer 에 대해서 fragment shader 에서 작업을 실행

```cpp
#version 400 core
layout (location = 0) out vec4 FragColor;
layout (location = 1) out vec4 BrightColor;

[...]

void main()
{            
    [...] // first do normal lighting calculations and output results
    FragColor = vec4(lighting, 1.0);
    // check whether fragment output is higher than threshold, if so output as brightness color
    float brightness = dot(FragColor.rgb, vec3(0.2126, 0.7152, 0.0722));
    if(brightness > 1.0)
        BrightColor = vec4(FragColor.rgb, 1.0);
    else
        BrightColor = vec4(0.0, 0.0, 0.0, 1.0);
```

- 기존에 랜더링 과정과 같은 작업을 수행해 FragColor 에 저장
- 해당 fragment 가 임계값을 넘어서는지 확인한 후 넘어서는 값이면 두번째 이미지인 BrightColor 에 저장

## gaussian blur

가우시안 블러

![Untitled](bloom%204efc599c9d574b2cbf28856652abd981/Untitled%201.png)

가우스 곡선을 사용해 target fragment 주위의 픽셀을 샘플링하여 가중치를 줌

- 2차원 가우스 곡선을 사용해 타겟 주위의 32x32 픽셀을 샘플링하면 총 1024번의 과정을 거쳐야 함
- 2차원 가우스 방정식은 수평, 수직 가중치로 나누어지며 이를 수평에 한번, 수직에 한번 실행하여 성능을 향상시킴
    - two-pass gaussian blur

![Untitled](bloom%204efc599c9d574b2cbf28856652abd981/Untitled%202.png)

```cpp
#version 400 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D image;
  
uniform bool horizontal;
uniform float weight[5] = float[] (0.227027, 0.1945946, 0.1216216, 0.054054, 0.016216);

void main()
{             
    vec2 tex_offset = 1.0 / textureSize(image, 0); // gets size of single texel
    vec3 result = texture(image, TexCoords).rgb * weight[0]; // current fragment's contribution
    if(horizontal)
    {
        for(int i = 1; i < 5; ++i)
        {
            result += texture(image, TexCoords + vec2(tex_offset.x * i, 0.0)).rgb * weight[i];
            result += texture(image, TexCoords - vec2(tex_offset.x * i, 0.0)).rgb * weight[i];
        }
    }
    else
    {
        for(int i = 1; i < 5; ++i)
        {
            result += texture(image, TexCoords + vec2(0.0, tex_offset.y * i)).rgb * weight[i];
            result += texture(image, TexCoords - vec2(0.0, tex_offset.y * i)).rgb * weight[i];
        }
    }
    FragColor = vec4(result, 1.0);
}
```

- 가우시안 블러를 만들어주는 fragment shader
- 상하 혹은 좌우 방향으로 가중치 만큼 흐리게 만듬

```cpp
unsigned int pingpongFBO[2];
unsigned int pingpongBuffer[2];
glGenFramebuffers(2, pingpongFBO);
glGenTextures(2, pingpongBuffer);
for (unsigned int i = 0; i < 2; i++)
{
    glBindFramebuffer(GL_FRAMEBUFFER, pingpongFBO[i]);
    glBindTexture(GL_TEXTURE_2D, pingpongBuffer[i]);
    glTexImage2D(
        GL_TEXTURE_2D, 0, GL_RGB16F, SCR_WIDTH, SCR_HEIGHT, 0, GL_RGB, GL_FLOAT, NULL
    );
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    glFramebufferTexture2D(
        GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, pingpongBuffer[i], 0
    );
}
```

- 하나의 프레임 버퍼에 두개의 이미지를 만들고 한쪽에서 다른쪽으로 블러효과를 주어 랜더링시키는 과정을 반복
- 시작할때 하나의 이미지를 채운 뒤 반대쪽으로 랜더링

```cpp
bool horizontal = true, first_iteration = true;
int amount = 10;
shaderBlur.use();
for (unsigned int i = 0; i < amount; i++)
{
    glBindFramebuffer(GL_FRAMEBUFFER, pingpongFBO[horizontal]); 
    shaderBlur.setInt("horizontal", horizontal);
    glBindTexture(
        GL_TEXTURE_2D, first_iteration ? colorBuffers[1] : pingpongBuffers[!horizontal]
    ); 
    RenderQuad();
    horizontal = !horizontal;
    if (first_iteration)
        first_iteration = false;
}
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

- 수평방향부터 시작해 수평, 수직을 번갈아 총 10번, 각각 5번씩 진행되어 5번의 가우시안 블러 효과를 부여

## blending both textures

HDR 과 블러효과가 적용된 이미지를 만든 뒤 두 이미지를 결합

```cpp
#version 400 core
out vec4 FragColor;
  
in vec2 TexCoords;

uniform sampler2D scene;
uniform sampler2D bloomBlur;
uniform float exposure;

void main()
{             
    const float gamma = 2.2;
    vec3 hdrColor = texture(scene, TexCoords).rgb;      
    vec3 bloomColor = texture(bloomBlur, TexCoords).rgb;
    hdrColor += bloomColor; // additive blending
    // tone mapping
    vec3 result = vec3(1.0) - exp(-hdrColor * exposure);
    // also gamma correct while we're at it       
    result = pow(result, vec3(1.0 / gamma));
    FragColor = vec4(result, 1.0);
}
```