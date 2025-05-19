# selector

**tag - > tag name**

- 해당 태그에 일괄적으로 효과 적용

**id -> #**

- 파일에 단 한번만 존재
- 한 태그에 여러 id 가 중복될 수 없음

**class -> .**

- 여러번 사용 가능 : 같은 효과를 주고싶은 태그에 중복 사용
- 한 태그에 여러 class 사용 가능 : 여러 효과를 중복 적용

**selector 적용의 우선순위**

- inline style > in-file style > out-file style
- id > class > tag
- !important
    - 어떠한 경우에도 제일 우선시 되는 스타일

**자손과 자식**

- 자손 선택자
    - element element ex)div a
    - 자식, 손자 등 하위의 모든 개체를 지칭
- 자식 선택자
    - element>elememt ex)div>a
    - 상위개체 바로 아래의 하위 개체만 지칭