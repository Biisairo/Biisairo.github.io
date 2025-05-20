# cubemap

2D 텍스처가 아닌 여러 텍스처가 하나로 매핑되어있는 큐브 모양

6개의 개별 2D 텍스처가 하나의 면을 구성하는 육면체의 큐브모양 텍스처 맵

0, 0, 0 좌표를 중심으로 하는 1x1x1 크기의 큐브로 방향벡터를 사용해 텍스처의 값을 샘플링함

## create cubemap

다른 텍스처와 같이 textureID 를 만들어 bind

id 에는 GL_TEXTURE_CUBE_MAP 을 bind

각 면은 glTexImage2D 함수를 사용해 만들며 target 에 각 면에 해당하는 texture target 을 넣어 적용

![Screen Shot 2023-09-25 at 2.00.46 PM.png](cubemap%209c86e0ad4f3e41c3a104403a18f28108/Screen_Shot_2023-09-25_at_2.00.46_PM.png)

### skybox

하늘 이미지 같은 이미지를 사용해 사용자의 주변을 둘러주어 배경을 만듬

skybox 를 랜더링 할 때에는 view 의 마지막 행, 렬을 0으로 만들어 view 행렬의 translate (이동)을 제거

### 최적화

skybox 를 랜더링 한 후 물체를 랜더링 하면 물체에 가려지는 skybox 부분에 대해 필요없는 계산이 들어감

물체를 먼저 랜더링 한 후 skybox 의 깊이를 무조건 맨 뒤로 밀어버리면 쓸데없는 랜더링이 필요하지 않게 됨

skybox 의 쉐이더에서 gl_Position 을 pos.xyww 로 설정

물체의 깊이는 z 에서 담당하며 실제로는 x, y, z 값을 w 로 나누어 실제 좌표를 구하게 됨

z 대신 w 값을 넣어 깊이가 무조건 1 로 설정되게 하면 해당 좌표는 무조건 맨 뒤에 위치하는 것으로 간주됨

깊이함수를 default 인 GL_LESS 대신 GL_LEQUAL 로 설정

### environment mapping

주위 환경을 단순 텍스처에 맵핑시켜 해당 텍스처를 사용한 굴절, 반사효과를 사용할 수 있음

- reflection
    - 카메라-물체 벡터를 법선백터에 반사시킨다음 (glsl 의 reflect 함수 사용) 해당 벡터를 skybox 에서 샘플링 하여 사용
- refraction
    - 카메라-물체 벡터를 물체의 법선에 대하여 일정 수치를 사용해 굴절시킨다음 (glsl 의  refract 함수 사용) 해당 벡터를 skybox 에서 샘플링 하여 사용
    

### dynamic environment maps

skybox 를 정적인 이미지가 아닌 실제 움직이는 장면을 사용하는 법

framebuffer 를 사용해 각 면의 텍스처를 만들고 해당 텍스처를 큐브맵에 저장한 뒤 랜더링