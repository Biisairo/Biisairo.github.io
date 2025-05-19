# blinn phong

## phong

phong effect 에서 specular 는 반사광의 각도와 시선의 각도의 차이를 이용해 빛의 강도를 결정

![Screen Shot 2023-10-05 at 12.17.43 PM.png](blinn%20phong%20e6efc7e91aa54f3bbbfdc5c693bf4360/Screen_Shot_2023-10-05_at_12.17.43_PM.png)

![Screen Shot 2023-10-05 at 12.18.30 PM.png](blinn%20phong%20e6efc7e91aa54f3bbbfdc5c693bf4360/Screen_Shot_2023-10-05_at_12.18.30_PM.png)

반사광의 각도와 시선의 각도의 차이가 90도를 넘어가면 해당 빛이 무효화 되는 현상이 발생

위 그림에서 가장자리 부분의 빛이 딱 끊기는 것

phong 의 단점을 해결하기 위한 빛 모델

![Screen Shot 2023-10-05 at 12.19.45 PM.png](blinn%20phong%20e6efc7e91aa54f3bbbfdc5c693bf4360/Screen_Shot_2023-10-05_at_12.19.45_PM.png)

반사광의 각도를 사용하는 것이 아는 빛과 시선의 중간 벡터를 사용

빛과 시선의 중간에 있는 벡터와 반사면의 법선백터의 각도차를 비교

각도가 작을수록 반사도가 높음 = 밝게보임

중간 벡터는 빛과 반사면사이의 벡터와 눈과 반사면 사이의 벡터를 합하여 구함