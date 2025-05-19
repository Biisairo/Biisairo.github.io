# Decorator function

**이미 있는 함수에 기능을 더하는 방법**

- 예시

def decoration_func(**function**):

def wrap_func():

#무언가 코드

**function()**

#무언가 코드

return wrap_func

@decoration_func

def **some_funtion**():

#무언가 코드

- some_function() 에 decoration 으로 꾸며진다

**wrap_func 에 인자를 넣어줄 때**

- 예시

def decoration_func(**function**):

def wrap_func(*args, **kwargs):

#무언가 코드 using args or keargs

**function()**

#무언가 코드 using args or keargs

return wrap_func

**인자를 받을 때**

- 예시

def main_func(받을인자)

def decoration_func(**function**):

def wrap_func():

| 코드 using 받을인자

**function()**

#무언가 코드 using 받을인자

return wrap_func

return decoration_func