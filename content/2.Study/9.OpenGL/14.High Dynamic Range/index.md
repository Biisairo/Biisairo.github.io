# HDR

## HDR (High Dynamic Range)

밝기, 색상값은 0~1 사이로 고정되어 저장되며 합계가 1 을 초과하는 경우에는 값이 1로 고정됨

값을 저장할 때에는 1 을 초과하는 값을 저장한 뒤 최종 랜더링 시 원래의 범위인 0~1 값으로 변환하는 방식으로 이를 해결

넓은 범위의 색상값을 사용해 어두운 부분과 밝은 부분을 모두 저장한 뒤 마지막에 HDR 값을 0~1 사이의 LDR 로 변환

HDR 을 LDR 로 변환하는 해당과정을 톤 매핑이라고 함

## floating point framebuffers

fragment shader 가 실행된 후 color 값이 [0, 1] 로 클램프 되지 않도록 해야 함

framebuffer 를 생성할 때 GL_RGB16F, GL_RGBA16F, GL_RGB32F, GL_RGBA32F 를 사용해 저장

프래임버퍼는 기본 범위인 0, 1 이외의 부동소수점 값을 저장할 수 있는 floating point framebuffer 가 됨

opengl 의 기본 framebuffer 는 pixle 당 8 비트를 사용

GL_RGB16F, GL_RGB32F 등을 사용하면 각각 16, 32 비트를 사용하며 높은 정밀도가 필요하지 않은 이상 16을 사용해도 충분함

해당 프레임버퍼를 사용해 랜더링 한 후 default framebuffer 를 사용해 해당 장면을 랜더링

- 이때 아무런 작업없이 바로 랜더링을 하게 되면 1 을 초과하는 color 를 1 로 클램프 하게 되기때문에 tone mapping 작업을 거쳐야함

## tone mapping

동적 범위인 floating point framebuffer 의 값을 [0, 1] 로 클램핑 하는 것이 아닌 해당 범위 내로 정규화 하는 작업

### Reinhard tone mapping

```cpp
void main()
{             
    const float gamma = 2.2;
    vec3 hdrColor = texture(hdrBuffer, TexCoords).rgb;
  
    // reinhard tone mapping
    vec3 mapped = hdrColor / (hdrColor + vec3(1.0));
    // gamma correction 
    mapped = pow(mapped, vec3(1.0 / gamma));
  
    FragColor = vec4(mapped, 1.0);
}
```

- HDR 의 값을 LDR 범위 내로 균등분산
- 해당 과정에서 gamma 작업을 같이 처리

```cpp
uniform float exposure;

void main()
{             
    const float gamma = 2.2;
    vec3 hdrColor = texture(hdrBuffer, TexCoords).rgb;
  
    // Exposure tone mapping
    vec3 mapped = vec3(1.0) - exp(-hdrColor * exposure);
    // Gamma correction 
    mapped = pow(mapped, vec3(1.0 / gamma));
  
    FragColor = vec4(mapped, 1.0);
}
```

- 노출값 exposure 을 사용
- 카메라의 노출과 비슷한듯

![Untitled](HDR%20fdb9bc32d88e4db6aed44c568e02dc8f/Untitled.png)