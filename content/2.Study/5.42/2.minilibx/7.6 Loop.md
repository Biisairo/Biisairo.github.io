# 6. Loop

# Hooking into loop

무한루프를 만들기 위해 `mlx_loop` 함수를 사용하며 여기에는 `mlx` 인자 하나만 들어가게 된다

```c
#include <mlx.h>int main(void){    void *mlx;    mlx = mlx_init();    mlx_loop(mlx);}
```

이렇게만 두면 아무 일도 일어나지 않기 때문에 `mlx_loop_hook` 함수를 이용한다

```c
#include <mlx.h>int render_next_frame(void *YourStruct);int main(void){    void    *mlx;    mlx = mlx_init();    mlx_loop_hook(mlx, render_next_frame, YourStruct);    mlx_loop(mlx);}
```

매 loop 마다 render_next_frame 이 호출되도록 한다