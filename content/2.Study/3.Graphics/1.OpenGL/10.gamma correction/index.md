# gamma correction

사람이 느끼는 빛의 강도 차이와 실제로 빛의 강도차이에는 차이가 있음

실제 빛의 강도는 2배가 될 때 방출하는 광자의 양이 2배가 된다는 뜻

![Screen Shot 2023-10-05 at 12.26.56 PM.png](gamma%20correction%2063fe389490414a419583b5260f71516f/Screen_Shot_2023-10-05_at_12.26.56_PM.png)

위의 그림은 사람이 느끼는 2배씩 밝아지는 빛

아래의 그림은 실제로 2배씩 밝아지는 빛

## gamma correction

모니터에 출력하기 전 모니터 감마의 역수를 최종 출력에 적용하는 것

감마값은 2.2 로 디스플레이가 가지는 평균적인 감마값

### opengl 내장 sRGB 프레임 버퍼

쉬운 방법이지만 제어력이 떨어짐

GL_FRAMEBUFFER_SRGB 를 사용

draw 명령으로 색이 color buffer 에 저장되기 전 sRGB 의 색공간의 색을 감마보정

sRGB 는 2.2 감마에 해당하는 색 공간

```cpp
glEnable(GL_FRAMEBUFFER_SRGB)
```

위 코드를 사용해 sRGB 를 사용

활성화 되면 자동적으로 감마보정이 수행

감마 보정은 한번만 되어야 함

- framebuffer 를 사용하는 경우
- framebuffer 로 랜더링을 할때는 원래의 선형 공간에서 작업을 진행
- 실제 모니터에 출력 할때만 감마 보정을 사용

### fragment shader 에서 감마보정을 수행

```cpp
float gamma = 2.2;
color.rgb = pow(color.rgb, vec3(1.0/gamma));
```

모든 물체를 그릴때 fragment shader 마다 설정을 해 주어야함

혹은 framebuffer 를 사용하여 한장의 이미지로 만들어서 해당 이미지를 출력할때 보정을 사용

### sRGB texture

랜더링 되는 이미지가 아닌 texture 이미지의 경우에는 작업자가 이미 sRGB 공간에서 작업하고 있게 됨

이때 해당 이미지를 다시 감마보정을 하면 두번의 감마보정이 들어가게 되어 너무 밝은 이미지가 랜더링 되게 됨

- texture 를 선형공간으로 변형
    - 위에서는 gamma 의 역수를 주어 감마보정을 했음
    - 이미 감마 보정이 되어있는 texture 이미지는 gamma 를 곱해 이미 들어가 있는 보정을 없애줌
    
    ```cpp
    float gamma = 2.2;
    vec3 diffuseColor = pow(texture(diffuse, texCoords).rgb, vec3(gamma));
    ```
    
    - 이후 최종단계에서 다시 감마보정을 사용
- texture 를 만들 때 내부 형식을 제공
    - glTexImage2D 를 사용해 texture 를 생성할 때 GL_SRGB, GL_SRGB_ALPHA 를 포맷으로 주어 해당 이미지가 sRGB 공간에 있다는 것을 명시
    - 오브젝트 채색에 사용되는 texture 는 sRGB 에, 반사맵, 노멀맵과 같은 texture 는 선형공간에 있음을 주의해야 함

## Attenuation

조명 감쇠

실제 물리적인 조명은 거리의 제곱에 반비례하여 감쇠함

```cpp
// quadratic
float attenuation = 1 / (distance * distance);
// linear
float attenuation = 1 / distance;
```

감마 보정이 없으면 이차 감쇠를 하면 너무 어두워짐

감마 보정을 하고 선형 감쇠를 하면 너무 밝아짐

감마 보정을 하고 이차감쇠를 하는것이 바람직

감마 보정이 불가능하면 선형 감쇠를 사용

![Screen Shot 2023-10-05 at 1.33.59 PM.png](gamma%20correction%2063fe389490414a419583b5260f71516f/Screen_Shot_2023-10-05_at_1.33.59_PM.png)