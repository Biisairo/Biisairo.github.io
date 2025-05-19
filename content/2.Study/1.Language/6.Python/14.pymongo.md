# pymongo

**클라이언트 접속**

- client = MongoClient(host=“IP”, port=“port”)
- 혼자할때는 : localhost
- mongodb로 약속 된 포트 : 27017

**데이터베이스 접속/생성**

- db = client.db_name
- db = client[“db_name”]

**콜랙션 접속.생성**

- collection =.db.collection_name
- collection = db[“collection_name”]

**CREATE**

- 한개
    - doc={“key”: “value”}
    - db.collection_name.insert_one(doc)
- 여러개
    - docs=[{“key”: “value”}, {“key”: “value”}]
    - db.collection_name.insert_many(docs)

**READ**

- 한개
    - something = db.collection_name.find_one({“key”: “value”})
- 여러개
    - something = list(db.collection_name.find({“key”: “value”}, {“_id”: False}))
    - .find 는 객체로 나오기때문에 list() 로 리스트화 해줘야한다
- 뒤의 {“_id”: False} 는 id 의 데이터는 보지 않겠다는 조건

**UPDATE**

- db.collection_name.update_one({“key”:“value”},{“$set”:{“key”:“value”}})
- 앞의 {“key”:“value”} 에는 바꾸고자 하는 데이터를 찾고
- 뒤의 {“$set”:{“key”:“value”}} 로 데이터를 바꾼다

**DELETE**

- db.collection_name.delete_one({“key”: “value”})