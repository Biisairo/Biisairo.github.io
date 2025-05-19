# flask-ckeditor

**본문글을 적는 form 을 만들어주는 라이브러리**

기본 구조

# ckeditor 를 불러와 준다

from flask_ckeditor import CKEditor, CKEditorField

# ckeditor 의 초기화

ckeditor = CKEditor(app)

# Form 을 만들 때 Field 를 CKEditorField 를 사용하여 만들어준다

body = CKEditorField(“Blog Content”, validators=[DataRequired()])

**HTML File**

# form 을 불러오기 전 ckedtor 을 불러오고 name 을 지정 해 준다

{{ ckeditor.load() }}

{{ ckeditor.config(name=‘body’) }}

{{ wtf.quick_form(form, novalidate=True) }}

**내용 추출**

- 기타 form 의 정보 추출과 같이
- body=request.form[“body”]
- 의 방법으로 하면 된다
- html 정보로 추출이되기 때문에 태그가 붙어서 나온다
- 이때 추출된 정보를 사용할 때 safe() filter 를 사용하면 제대로된 본문이 나오게 된다
- {{ post.body }} 가 아닌 {{ post.body**|safe** }}