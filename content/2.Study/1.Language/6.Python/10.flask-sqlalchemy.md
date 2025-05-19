# flask-sqlalchemy

**sql 를 객체 관계형 매핑(ORM) 으로 flask 에서 컨트롤 할 수 있게 해주는 라이브러리**

- 필드 = 객체 속성
- 클래스 = Table
- 클래스 객채 = 데이터의 각 행
- 으로 대응 된다

**db 생성 및 데이터 추가**

**기본 구조**

# flask 로 app 을 만들고

app = Flask(__name__)

# app 하위에 db 가 만들어질 경로와 db 이름은 설정해준 다음

app.config[‘SQLALCHEMY_DATABASE_URI’] = ‘sqlite:///new-books-collection.db’

# app 안에 db 를 생성한다

db = SQLAlchemy(app)

# Book 이라는 이름의 Table 을 생성 해 주고

class Book(db.Model):

# 각각의 행을 만들어준다

id = db.Column(db.Integer, primary_key=True)

title = db.Column(db.String(250), unique=True, nullable=False)

author = db.Column(db.String(250), nullable=False)

rating = db.Column(db.Float, nullable=False)

# 옵션 : 아래 함수는 각각의 개체를 print 할때 제목으로 구별할 수 있게 한다

def __repr__(self):

return f’<Book {self.title}>’

# Table 을 생성해준다

db.create_all()

# 개체(열) 데이터 추가

# new_book 이라는 이름의 새 데이터를 만들어 준 다음

new_book = Book(id=1, title=“Harry Potter”, author=“J. K. Rowling”, rating=9.3)

# new_book 을 db에 추가시키고

db.session.add(new_book)

# db 를 commit 해 수정사항을 저장 해 준다

db.session.commit()

**CRUD**

**C : Create**

- new_book = Book(title=“Harry Potter”, author=“J. K. Rowling”, rating=9.3)
- db.session.add(new_book)
- db.session.commit()

**R : Read**

- 모든 레코드 읽기
    - all_books = db.session.query(Book).all()
- 특정 레코드 읽기
    - book = Book.query.filter_by(title=“Hary Potter”).first()

**U : Update**

- 특정 레코드 업데이트하기
    - book_to_update = Book.query.filter_by(title=“Harry Potter”).first()
    - book_to_update.title = “Harry Potter and the Chamber of Secrets”
    - db.session.commit()
- id 로 레코드 업데이트 하기
    - book_id = 1
    - book_to_update = Book.query.get(book_id)
    - book_to_update.title = “Harry Potter and the Goblet of Fire”
    - db.session.commit()

**D : Delete**

- 기본키로 레코드 삭제하기
    - book_id = 1
    - book_to_delete = Book.query.get(book_id)
    - db.session.delete(book_to_delete)
    - db.session.commit()
- 제목 및 다른 속성값으로 조회하여 삭제할 수도 있다

**관계형 데이터베이스**

- 두개 table 을 연결시켜 한개의 테이블이 다른 한개의 테이블을 참조하도록 만드는 것
- 두 테이블의 양방향 관계형 데이터베이스 만드는 법
- parent 가 될 테이블을 만들 때

class Parent(db.Model):

id = db.Column(db.Integer, primary_key=True)

.

.

some_1 = relationship(“Child”, back_populates=“some_2”)

- child 가 될 테이블을 만들 때

class Child(db.Model):

id = db.Column(db.Integer, primary_key=True)

.

.

some_2_id = db.Column(db.Integer, ForeginKey=(“parent.id”)

some_2 = relationship(“Child”, back_populates=“some_1”)

- 각 테이블이 서로를 객체의 인자로 두고 서로를 참조하게되며
- 이때 child 의 ForeginKey 는 parent 의 id 를 레퍼런스로 만들어진다
- 자식 관계에 있는 table 데이터를 생성할 때 마다 부모객체의 데이터를 넣어주어야 한다
- 부모객체를 넣어주면 자동으로 부모객체츷 참조한 ForeginKey 를 가진 id 가 생성이 된다