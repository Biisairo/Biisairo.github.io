# parallax mapping

높이 맵을 사용해 실제 정점의 변화없이 높이 차이를 볼 수 있도록 하는 mapping 방식

![Screen Shot 2023-10-12 at 3.39.52 PM.png](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Screen_Shot_2023-10-12_at_3.39.52_PM.png)

viewVector 인 V 가 실제 벽돌의 위치인 B 를 보려고 할 때 실제 물체에는 굴곡이 없는 검은색의 평면이기 때문에 B 가 아닌 A 가 보이게 됨

parallax mapping 은 B 를 보려고 할때 A 의 좌표를 B 로 오프셋 하셔 굴곡이 있는 것 처럼 보이게 하는 것이 목표

![Screen Shot 2023-10-12 at 3.43.11 PM.png](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Screen_Shot_2023-10-12_at_3.43.11_PM.png)

벡터 P 를 구하는 법

- vertex A 에서 eye 로 이어지는 view vector 를 구한 뒤 A 정점에서의 높이인 H(A) 로 스케일링
- 실제 값과는 다를 수 있으나 근사값을 가지게 됨

![Untitled](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Untitled.png)

- 높이 값이 급격하게 변하는 경우 이 방식을 사용할 경우 비현실적인 결과가 나오게 됨
- 물체가 움직이면 P 의 좌표를 찾기 어려워 지며 이는 접선공간을 사용하여 해결

## parallax mapping

실제 높이 맵은 평면을 기준으로 아래로 들어가는 역방저

![Screen Shot 2023-10-12 at 3.54.03 PM.png](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Screen_Shot_2023-10-12_at_3.54.03_PM.png)

점 A 에서 V 벡터의 반대방향으로 A 의 높이만큼 위치한 곳의 texture 를 사용

```cpp
// fragment shader

vec2 ParallaxMapping(vec2 texCoords, vec3 viewDir)
{ 
    float height =  texture(depthMap, texCoords).r;    
    vec2 p = viewDir.xy / viewDir.z * (height * height_scale);
    return texCoords - p;    
}

...
vec2 texCoords = ParallaxMapping(fs_in.TexCoords,  viewDir);
if(texCoords.x > 1.0 || texCoords.y > 1.0 || texCoords.x < 0.0 || texCoords.y < 0.0)
	discard;
...
```

- 현재의 texcoord 와 view 벡터, depth map 을 사용해 실제 텍스처에서 사용할 texcoord 를 구한 뒤 해당 좌표를 사용해 texture mapping 을 함
- 이후 텍스처의 좌표범위를 넘어가는 값이 샘플링된 경우 discard 를 사용하여 제거

## steep parallax mapping

![Screen Shot 2023-10-12 at 4.56.37 PM.png](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Screen_Shot_2023-10-12_at_4.56.37_PM.png)

parallax mapping 도 결국 평면상에서의 mapping 이며 근사치를 사용한 mapping 이기때문에 왜곡이 생김

steep parallax mapping 은 표본 B 를 찾기위해 여러 샘플을 사용하여 가장 가까운 값을 찾는 방법

전체 깊이범위를 동일한 깊이의 여러 레이어로 나누는 것

![Untitled](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Untitled%201.png)

- 깊이 레이어를 지나치며 각 레이어에대해 depth map 에 저장된 값과 비교
- 레이어의 깊이값이 depth map 에 저장된 값보다 작으면 P 점이 표면 위에 있다는 것
- 레이어의 깊이값이 저장된 값보다 클때까지 과정을 반복
- 위 그림에서
    - T0 부터 탐색을 시작
    - T1, T2 는 깊이값이 저장된 값보다 위에 있으므로 지나침
    - T3 의 값이 깊이값보다 아래 있으므로 해당 값을 사용

```cpp
vec2 SteepParallaxMapping(vec2 texCoords, vec3 viewDir)
{
    const float minLayers = 8.0;
    const float maxLayers = 32.0;
    float layer = mix(maxLayers, minLayers, abs(dot(vec3(0.0, 0.0, 1.0), viewDir))); 

    float layerDepth = 1.0 / layer;
    float currentLayerDepth = 0.0;

    vec2 p = viewDir.xy * height_scale;
    vec2 deltaTexCoords = p / layer;

    vec2  currentTexCoords = texCoords;
    float currentDepthMapValue = texture(depthMap, currentTexCoords).r;
    
    while(currentLayerDepth < currentDepthMapValue)
    {
        currentTexCoords -= deltaTexCoords;
        currentDepthMapValue = texture(depthMap, currentTexCoords).r;  
        currentLayerDepth += layerDepth;  
    }

    return currentTexCoords;
}
```

- layer 의 수는 보는 각도에 따라서 plane 의 normal 에 가깝게 볼수록 적은샘플링을 하도록 함

위와같은 방법을 사용하면 layer 사이의 층이 생기게 되며 가까운 두 layer 를 보간해 이를 해결

- relief parallax mapping
    - 정확한 결과
- parallax occlusion mapping
    - 높은 성능

### parallax occlusion mapping

steep parallax mapping 과 같은 방법을 사용

충돌이 일어나면 이후 첫번째 layer 의 값을 가져오는 것이 아닌 충돌 전후의 값을 사용해 선형보간하게 됨

![Untitled](parallax%20mapping%200a2b30efc47c412fa754f6e75b7f6694/Untitled%202.png)

```cpp
vec2 ParallaxOcclusionMapping(vec2 texCoords, vec3 viewDir)
{
    const float minLayers = 8.0;
    const float maxLayers = 32.0;
    float layer = mix(maxLayers, minLayers, abs(dot(vec3(0.0, 0.0, 1.0), viewDir))); 

    float layerDepth = 1.0 / layer;
    float currentLayerDepth = 0.0;

    vec2 p = viewDir.xy * height_scale;
    vec2 deltaTexCoords = p / layer;

    vec2  currentTexCoords = texCoords;
    float currentDepthMapValue = texture(depthMap, currentTexCoords).r;
    
    while(currentLayerDepth < currentDepthMapValue)
    {
        currentTexCoords -= deltaTexCoords;
        currentDepthMapValue = texture(depthMap, currentTexCoords).r;  
        currentLayerDepth += layerDepth;  
    }

    vec2 prevTexCoords = currentTexCoords + deltaTexCoords;

    float afterDepth  = currentDepthMapValue - currentLayerDepth;
    float beforeDepth = texture(depthMap, prevTexCoords).r - currentLayerDepth + layerDepth;
    
    float weight = afterDepth / (afterDepth - beforeDepth);
    vec2 finalTexCoords = prevTexCoords * weight + currentTexCoords * (1.0 - weight);

    return finalTexCoords; 
}
```