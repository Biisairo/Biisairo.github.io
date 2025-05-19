# flask-bootstrap

**flask template 안에서 부트스트랩을 사용할 수 있도록 해주는 라이브러리**

- **기본구성**
    - …
    - from flask_bootstrap import Bootstrap
    - …
    - app = Flask(__name__)
    - Bootstrap(app)
- 라이브러리 내에 **기본적**으로 베이스 템플릿이 포함되어있으며, 이 템플릿을 상속받는 명령어는 아래 이다
    - {% extends “bootstrap/base.html” %}