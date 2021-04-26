# mongoimport 로 덤프파일 import 하기

<img src="./img/mongo-icon-official-3.png" alt="이미지" style="zoom:25%;" />



# 참고자료

- [MongoDB Guides > Import Data into MongoDB](https://docs.mongodb.com/guides/server/import/)
- [샘플 데이터 - inventory.crud.json](https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/inventory.crud.json)



# 샘플데이터

```json
{ "item": "journal", "qty": 25, "size": { "h": 14, "w": 21, "uom": "cm" }, "status": "A" }
{ "item": "notebook", "qty": 50, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "A" }
{ "item": "paper", "qty": 100, "size": { "h": 8.5, "w": 11, "uom": "in" }, "status": "D" }
{ "item": "planner", "qty": 75, "size": { "h": 22.85, "w": 30, "uom": "cm" }, "status": "D" }
{ "item": "postcard", "qty": 45, "size": { "h": 10, "w": 15.25, "uom": "cm" }, "status": "A" }
```



# mongoimport

위의 json 데이터는 `sample-data.json`  라는 이름의 파일로 미리 `/data/db/`  디렉터리에 복사해두었다. 이 상태에서 아래의 명령어를 내려주었다.

```bash
$ mongoimport --db test --collection inventory --authenticationDatabase admin --username mongoadmin --password 1111 --drop --file /data/db/sample-dump.json
```



# 결과확인

```javascript
$ mongo -u mongoadmin -p

// test 디비 전환
> use test
switched to db test

// inventory collection 내의 모든 데이터 조회
> db.inventory.find().pretty()
{
	"_id" : ObjectId("605f27b48614e26119137b9b"),
	"item" : "journal",
	"qty" : 25,
	"size" : {
		"h" : 14,
		"w" : 21,
		"uom" : "cm"
	},
	"status" : "A"
}
{
	"_id" : ObjectId("605f27b48614e26119137b9c"),
	"item" : "postcard",
	"qty" : 45,
	"size" : {
		"h" : 10,
		"w" : 15.25,
		"uom" : "cm"
	},
	"status" : "A"
}
{
	"_id" : ObjectId("605f27b48614e26119137b9d"),
	"item" : "notebook",
	"qty" : 50,
	"size" : {
		"h" : 8.5,
		"w" : 11,
		"uom" : "in"
	},
	"status" : "A"
}
{
	"_id" : ObjectId("605f27b48614e26119137b9e"),
	"item" : "paper",
	"qty" : 100,
	"size" : {
		"h" : 8.5,
		"w" : 11,
		"uom" : "in"
	},
	"status" : "D"
}
{
	"_id" : ObjectId("605f27b48614e26119137b9f"),
	"item" : "planner",
	"qty" : 75,
	"size" : {
		"h" : 22.85,
		"w" : 30,
		"uom" : "cm"
	},
	"status" : "D"
}
```







