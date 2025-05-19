# bs4

**데이터 가공**

- soup = BeautifulSoup(response.content, “html.parser”)
- requests 로 가져온 response 데이터를 content 로 형변형 후 html 형식으로 변환

**정보 추출**

- class name 을 사용해서 찾기
    - tag = soup.find(name = “tag name”, 속성 = “속성이름”)
    - tag_all = soup.find_all(name = “tag name”, 속성 = “속성이름”)
    - class 를 확인하는 경우, python 의 class 선언과 겹치므로 class_ 를 사용
- selector 를 사용해서 찾기
    - soup.select(selector = “선택자”)
    - soup.select_one(selector = “선택자”)
- 찾아낸 태그에서 정보 추출하기
    - 태그 이름 추출하기
        - tag.name
    - 태그 내용 추출하기
        - tag.getText()
    - 태그 속성 추출하기
        - tag.get(“속성”)