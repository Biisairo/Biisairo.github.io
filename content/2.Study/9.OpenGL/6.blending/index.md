# blending

RGB 를 RGBA 의 vec4 로 처리하며 4번째 값을 투명도로 사용

1 일 경우 투명도는 0, 0 일경우 투명도는 1

## 투명도 100을 처리하는 방법

투명도 값이 특정 수치 이하일 때 fragment 를 버리는 방법

```glsl
if (color.a < 0.1)
	discard;
```

## blending

```glsl
glEnable(GL_BLEND)
```

를 사용해 반투명 이미지를 블랜드 할 수 있음

색이 blend 되는 방법

- resColor = sourceColor * sourceAlpha + destinationColor * destinationAlpha

source 의 투명도가 0.6 이면 destination 의 투명도를 1 - sourceAlpha 로 해 비율을 맞춰주면 좋음

해당 요소를 결정하는 함수가 glBlendFunc(GLenum sfactor, GLenum dfactor)

![스크린샷 2023-09-22 오후 1.35.27.png](blending%20eb1a5718dfe740108aa56ade0c71a504/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-09-22_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_1.35.27.png)

위와같은 알파값, 1 - 알파값을 얻으려면 각각 GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA 를 사용해 주면 됨

glBlendFuncSeperate() 를 사용해 다른 옵션을 추가로 넣어줄 수 있음

glBlendEquation(GLenum mode)

source 와 destination 사이의 연산자를 변경하여 다른 계산을 할 수 있음

- GL_FUNC_ADD
    - res = src + dst
- GL_FUNC_SUBTRACT
    - res = src - dst
- GL_FUNC_REVERSE_SUBTRACT
    - res = dst - src
- GL_MIN
    - res = min(src, dst)
- GL_MAX
    - res = max(src, dst)

## 반투명 물체의 랜더링

반투명한 물체끼리는 랜더링 하게되면 depth test 에 의해 가려지게 됨

이를 해결하기 위해 반투명한 물체는 depth test 를 하지 않고 깊이를 고려해 순서대로 그려야 함

순서

1. 불투명한 물체를 전부 랜더링
2. 투명한 물체들을 깊이순서에 맞게 정렬
3. 투명한 물체들을 먼 순서부터 랜더링