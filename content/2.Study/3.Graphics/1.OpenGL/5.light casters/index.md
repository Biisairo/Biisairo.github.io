# light casters

## directional light

광원과의 거리가 멀때 광원은 물체에 거의 평행하게 들어옴

물체와 광원이 거의 무한히 멀리있을 때 directional light, 지향형 광원 이라고 부르며 이는 모든 광선이 동일한 방향으로 들어옴

광원의 위치에 독립적

예시 : 태양빛

모든 방향에 대해서 동일하게 들어오므로 광원을 위치가 아닌 방향벡터를 사용해 표현

```jsx
struct light {
	vec3 direction; // not use position

	vec3 ambient;
	vec3 diffuse;
	vec3 specular;
}
```

일반적으로 direction 은 물체로부터 광원의 방향으로 넣기 때문에 -1 을 곱하여 방향을 바꿔준 뒤 normalize 해서 사용

## point light

점 광원

빛이 특정 위치에 있는 경우

각도에따라, 거리에 따라 빛의 양이 달라진다

### attenuation

감쇠

거리에 따라 빛의 세기를 감소시키는 것

일반 선형방정식을 사용하면 인위적인 느낌이 나게됨

광원의 감쇠 정도를 구하는 식

![Untitled](light%20casters%208e4ccb48a7c74e5186c67a6a19c01531/Untitled.png)

```jsx
Kc = 상수항 // 대게 1로 유지, 분모가 1보다 작아지지 않도록 하기위한 요소
Kl = 선형항 // 선형방식으로 거리에따라 감소시키는 항
Kq = 이차항 // 거리의 제곱에 곱해져 커질수록 크기를 급감시킴
d = 거리
```

위 식을 사용하면 처음에는 거리에따라 빠르게, 뒤에는 천천히 감소하는 그래프가 나옴

아래 표를 사용하여 보통 32-100 정도의 거리값을 사용

![Untitled](light%20casters%208e4ccb48a7c74e5186c67a6a19c01531/Untitled%201.png)

![Untitled](light%20casters%208e4ccb48a7c74e5186c67a6a19c01531/Untitled%202.png)

## spotlight

광원을 중심으로 모든 방향으로 뻗어나가는 point light 와 달리 광원에서 특정 방향으로만 빛을 쏘는 광원

광원의 좌표, 방향, 반경을 지정하는 차단각도로 표시

```jsx
struct light {
	vec3 position;
	vec3 direction;
	float cutoff;
}
```

cutoff 각도를 둘로 설정하여 두 각도 사이에 있을때는 부드러운 감소효과를 구현

```jsx
I = θ - γ / ϕ − γ
I = 가중치
θ = 물체와 direction 의 각도
ϕ = 내부원뿔의 cutoff 각도
γ = 외부원뿔의 cutoff 각도
```