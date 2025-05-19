# 2. Getting started

# Install

minilibx 을 사용하기위해

- CoCoa : MacODX (AppKit)
- OpenGL

가 필요하며 적절한 link 를 해 주어야 한다

### objects 파일을 생성할 시 makefile 에 몇몇 규칙을 생성한다

```makefile
%.o : %.c    $(CC) -Wall -Wextra -Werror -Imlx -c $< -o $@
```

### 내장 macOS API 연결

```makefile
$(NAME) : $(OBJ)    $(CC) $(OBJ) -Lmlx -lmlx -framework OpenGL -framework AppKit -o $(NAME)
```

build 대상과 동일한 디렉토리에 libmlx.dylib 가 필요하다 - 동적라이브러리 이기 때문…?

# Initialization

### MiniLibX 로 무언가를 하기 위해 `<mlx.h>` 를 include 한다

### 초기화를 위해 `mlx_init` 함수를 실행시킨다

해당 함수는 그래픽 시스템과의 연결을 만든 후 `void *`를 리턴한다 해당 포인터는 현재 MLX 객채들의 주소를 가진다**(의역)** 해당 함수만으로는 아무 작업이 일어나지 않는것이 맞으니 놀라지 말것

```c
#include <mlx.h>int main(){    void *mlx;    mlx = mlx_init();}
```

### 화면을 띄우기 위해 `mlx_new_window` 함수를 사용한다

해당 함수는 생성된 윈도우의 주소를 반환한다 인자로 높이, 너비, 이름을 받는다

### window 의 랜더링을 위해 `mlx_loop` 함수를 사용한다

```c
#include <mlx.h>int main(){    void *mlx;    void *mlx_win;    mlx = mlx_init();    mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!");    mlx_loop(mlx);}
```

# Writing pixels to a image

픽셀 하나를 올리는걸 시도한다 방법은 다양하며 최적화를 잘 해야 한다 `mlx_pixel_put` 함수는 매우 느리다 - 해당 함수는 랜더링이 끝나는걸 기다리지 않고 그냥 픽셀을 올리려고 시도하기때문이다

이런 이유때문에 우리는 이미지에 넣을 픽셀들을 buffer 시킬 필요가 있다

### 일단 mlx 가 요구하는 이미지의 타입을 이해해야 한다

이미지 작업을 위한 변수들을 만들어준다 `bpp` : bit per pixel - 픽셀은 기본적으로 4 byte 의 `int` 이다 - 하지만 이를 small-endian (little-endian) 으로 처리할 수 있다 - 이것은 8 bit 를 사용한다

### 이미지 초기화를 위해 `mlx_new_image` 를 사용한다

```c
#include <mlx.h>int main(void){    void    *img;    void    *mlx;    mlx = mlx_init();    img = mlx_new_image(mlx, 1920, 1080);}
```

픽셀을 그리기 위해서 우리는 픽셀의 bytes 가 그려질 주소값을 알아야 한다

```c
#include <mlx.h>typedef struct s_data{    void *img;    char *addr;    int bits_per_pixel;    int line_length;    int endian;} t_data;int main(){    void *mlx;    t_data img;    mlx = mlx_init();    img.img = mlx_new_image(mlx, 1920, 1080);/* 이미지를 만든 다음** mlx_get_data_addr 함수를 호출한다** 이 함수에는 bits_per_pixel, line_length, endian 이 들어간다** 이후 현재 데이터의 주소에 따라 적절히 설정된다 */    img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length, &img.endian);}
```

bits_per_pixel, line_length, endian 이 주소값으로 넘겨진다 이 값들은 mlxlibx 에 의해 적절한 값이 들어가게 된다

이제 이미지의 주소를 가지고있지만 아직 픽셀은 없다 작업을 시작하기 전에 bytes 가 정렬되지 않은걸 이해해야 한다 즉 설정된 `line_length` 는 실제 창의 너비와 같지 않을 수 있다 언제나 `mlx_get_data_addr` 을 사용해 얻은 `line_length` 를 사용해 메모리의 편차(offset)를 계산하여야 한다 - 해당 계산은 아래 식을 따른다

```c
int offset = (y * line_length + x * (bits_per_pixel / 8));
```

이제 우리는 어디에 써야할지 알고 `mlx_pixel_put`을 훨씬 빠른 속도로 따라하는 함수를 작성할 수 있다

```c
typedef struct  s_data {    void    *img;    char    *addr;    int     bits_per_pixel;    int     line_length;    int     endian;}               t_data;void    my_mlx_pixel_put(t_data *data, int x, int y, int color){    char    *dst;    dst = data->addr + (y * data->line_length + x * (data->bits_per_pixel / 8));    *(unsigned int*)dst = color;}
```

하지만 이 방식은 문제가 있다 이미지가 실시간으로 화면에 표현되기 때문에 같은 그림을 바꾸는 것은 화면을 찢어버리는 현상이 생긴다 프래임을 임시로 보관할 두개 이상의 이미지를 만들어야한다 임시 이미지를 변화시키면 현재 보여지는 이미지를 변화시키지 않아도 된다

# Pushing images to a window

### 만들어진 이미지를 윈도우에 올리기 위해 `mlx_put_image_to_window`를 사용한다

이제 우리는 이미지를 만들었다 우리가 해당 이미지를 보기 위해서 이미지를 화면 창에 밀어주어야 한다 (5, 5)위치에 픽셀을 넣는 것을 가정 해 보자

```c
#include <mlx.h>typedef struct  s_data {    void    *img;    char    *addr;    int     bits_per_pixel;    int     line_length;    int     endian;}               t_data;int main(void){    void    *mlx;    void    *mlx_win;    t_data  img;    mlx = mlx_init();    mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!");    img.img = mlx_new_image(mlx, 1920, 1080);    img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length,                                &img.endian);    my_mlx_pixel_put(&img, 5, 5, 0x00FF0000);    mlx_put_image_to_window(mlx, mlx_win, img.img, 0, 0);    mlx_loop(mlx);}
```

이때 `0x00FF0000` 은 `ARGB(0, 255, 0, 0)` 이다