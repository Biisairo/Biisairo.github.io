# Selenium

**Chrome Driver**

- selenium 과 chrome browser 를 연결시켜주는 역할

**문법**

- from selenium import webdriver
- 크롬드라이버 연결
    - driver = webdriver.Chrome(executable_path=“chrome_driver_path”)
- URL 불러오기
    - driver.get(“URL”)
- 열린 특정 탭 닫기
    - driver.close()
- 모든 탭 닫기
    - driver.quit()
- 찾기
    - something = driver.find_element_by…..(*args)
    - something = driver.find_elements_by…..(*args)
    - 이때 something 은 selenium 객체로 저장된다
- 찾은 객체에서 정보 추출
    - something.XXX
    - .get_attribute()
    - .text
    - .tag_name 등등…
- 클릭
- 클릭할 요소를 찾아서 저장한 뒤 ( something = driver.find….) : find_element_by_link_text() 를 쓰면 쉽다
- something.click()
- 타이핑
    - 타이핑할 요소를 찾아서 저장한 뒤 ( something = driver.find….)
    - something.send_key(“type_content”)
    - 토글키 사용
        - from selenium.webdriver.common.keys import Keys
        - Keys.ENTER, Keys.SHIFT 등으로 사용
- 탭 제어
    - 탭 선택
        - driver.window_handles[num]
        - 1번 탭이 0, 2번 탭이 1 …
    - 탭 이동
        - driver.switch_to.window(탭)
        - 탭 에는 위의 탭 선택 명령어를 넣으면 된다
    - Example
        - 3번 탭으로 가라
        - river.switch_to.window(driver.window_handles[2])
- javascritp 사용
- driver.execute_script(“code”)
- 해당 함수 안에 자바스크립트 코드를 써 넣으면 자바스크립트가 실행된다
- 페이지 스크롤하기 예시
    - driver.execute_script(“window.scrollTo(Y1, Y2)”)
    - (0, document.body.scrollHeight) 를 넣어 시작부터 끝까지 스크롤 할 수 있다
- 특정 요소로 포커스
    - move_to_element()
    - 코드 안에 특정 요소를 넣으면 해당 요소가 있는 화면으로 스크롤이 움직여 포커스 된다