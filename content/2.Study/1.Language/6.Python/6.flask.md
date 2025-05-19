# flask

**python 의  micro web framwork**

**기본골격**

from flask import Flask

app = Flask(__name__)

@app.route(‘/’)

def index():

return “welcome”

if __name__ == “__main__”:

app.run()

**app = Flask(__name__)**

- app 이라는 이름으로 Flask 를 실행 시킨다
- 이때 패키지의 이름을 __name__ 으로 한다

**__name__**

- print(__name__) 을 해보면 __main__ 이 출력된다
- 다른 모듈에서 임포트 하여 출력하면 파일이름으로 출력된다

**@app.route(‘/’)**

- decorator 함수
- 아래의 함수에 기능을 부여한다
- 여기서는 아래 함수가 실행되는 경로를 부여한다

**def XXX():**

- 해당 경로로 진입했을 시 실행되는 함수
- return 값이 브라우저에 출력된다

**if __name__ == “__main__”:**

**app.run()**

- 이 파일이 다른 모듈에 임포트 된 것인지 아닌지를 판별하는 구문
- 이 모듈이 다른곳에 임포트 된 것이 아닌 자체적으로 실행되었다면 Flask 를 작동시킨다
- app.run() 안에는 호스트와 포트, 디버그설정등이 들어간다
- 디버그 모드를 True 로 할 경우 저장할 때 마다 서버가 reload 된다

**라우팅에서의 변수지정**

- **@app.route(‘/’)**
- 에서 괄호안에 변수를 지정할 때
- **@app.route(’/")**
- **def func(value)**
- 처럼 꺾쇠로 받고 함수의 인풋값으로 사용
- 변수는 기본적으로 str 형식
- 다른형식의 변수를 사용할 경우
- 등으로 지정을 해 주어야 한다

**디버그모드**

- 버그가 발생했을 시 브라우저 창에 에러페이지가 뜬다
- 에러페이지의 에러를 누르면 인증창이 뜨는데 이때 인증은 개발툴의 콘솔창에 Debugger PIN 으로 가능하다
- 인증이 되면 에러 아래 콘솔창이 뜨며 이 콘솔창으로 테스트를 할 수 있다

**html template 불러오기**

- app.py 가 들어있는 폴더 안에 templates 라는 이름의 하위 폴더 생성 (이름 중요!!)
- templates 폴더 안에 html 파일 생성
- app.py 에서 return 에
- render_template(“XXX.html”)
- 로 불러오기

**static(정적) file 불러오기**

- app.py 가 들어있는 폴더 안에 static 라는 이름의 하위 폴더 생성 (이름 중요!!)
- static 폴더안에 파일 넣기
- 불러올때는
- static/file_name
- 으로 불러오기
- 사진, css, 비디오 등등…

**Hard Reload**

- 크롬에서는 static 파일을 캐시로 저장하기때문에 static 폴더안의 내용물을 바꾸어도 바로 변경사항이 반영되지 않는다
- 새로고침 버튼을 shift + click 하면 hard reload 가 진행되며 캐시를 모두 제거하고 다시 불러온다

**url_for()**

- url 값을 가져온다
- 괄호 안에 route 함수값을 넣으면 해당 함수가 가지는 주소를 가져온다
- 추가로 변수를 넣으면 변수를 포함한 주소값을 가져온다
- GET 방식으로 보낼때
    - 추가된 변수는 url? 뒤의 값으로 들어간다
    - ex) url_for(“func”, id=“12”) -> func_url?id=12

**넘어온 정보 받기**

- method 로 넘어온 정보를 받는다
- 기본구조
    - **@app.route(“/contact”, methods=[“METHOD”]**
- METHOD 에 GET 또는 POST 등 method 를 대문자로 적는다
- 이때 method 매개변수는 dict 형으로 저장되므로 여러 메소드로 수신이 가능하다
    - ex) @app.route(“/contact”, methods=[“GET”, “POST”]

**request**

- requests 라이브러리와 같지 않으니 주의!!
    - form flask import request
- method 로 넘어온 정보를 처리하는데 사용된다
- 넘어온 method 방식 확인
    - request.method
        - ==“POST”
        - ==“GET”
- 넘어온 정보 확인
    - POST 방식
        - request.form
            - ex) request.form[“name”]
    - GET 방식
        - request.args.to_dict()
        - 으로 dict 형식으로 변환 하여 사용
        - request.args.get(“KEY”)
        - 으로 키를 바로 넣어 value 를 받아 사용

**redirect**

- redirect(“
    
    ”)
    
- 해당 주소로 바로 접속한다
- url_for() 을 사용해 다른페이지로의 접근이 가능하다

**send_from_directory()**

- 정적(static) 파일을 가져와 클라이언트로 보내준다 - 즉 해당파일을 다운로드 하게된다
- 괄호 안에는 directory 와 filename 이 들어간다
- ex) send_from_directory(“/Users/yun/Desktop/Coding”. “chromedriver”)

**flask message**

# 특이사항이 있을 시 flash meassage 를 보낼 수 있다

flash(“message”)

# url 로 연결

return redirect(url_for(“func”))

# url 의 html 함수에서  아래의 방법으로 메세지를 받는다

{% with messages = get_flashed_messages() %}

{% if messages %}

{% for message in messages %}

{{ message }}

{% endfor %}

{% endif %}

{% endwith %}