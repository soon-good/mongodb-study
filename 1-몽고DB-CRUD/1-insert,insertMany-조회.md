# 몽고DB - insert, insertMany 

# 참고자료

[맛있는 MongoDB, 비제이퍼블릭, 정승호 저](http://www.yes24.com/Product/Goods/85011885)



# 1. 데이터베이스 상세 조회

- db.getCollectionInfos()
  - 현재 데이터베이스의 컬렉션들의 정보를 리스트로 반환한다.
  - 이름과 타입, UUID 정보를 얻는다.  

  


db.serverStatus()

- 호스트, 프로세스 id, Lock 옵션, 스토리지 엔진 이름, 스토리지 엔진 통계와 같은 정보를 제공

  
  

db.status()

- 데이터베이스 내의 컬렉션 뷰, 오브젝트의 갯수와 크기에 대한 통계를 제공한다.

  
  

# 2. 컬렉션 상태 조회

## 주요 명령어들

db.collection.isCapped()

- 컬렉션이 Capped 컬렉션이면 true 값을 반환한다.  

  


db.collection.latencyStats()

- 컬렉션의 지연 시간 통계를 보여준다.

  


db.collection.stats()

- 데이터베이스의 stats와 같이 컬렉션의 크기, 도큐먼트 갯수 및 스토리지 엔진의 통계를 제공한다.

  


db.collection.storageSize()

- 컬렉션 스토리지의 크기를 반환한다.

  


db.collection.totalIndexSize()

- 컬렉션 인덱스 크기를 반환한다.

  


db.collection.totalSize()

- 컬렉션의 스토리지와 인덱스 크기의 합을 반환한다.

  

## 예제

```javascript
# mongo -u mongoadmin -p
...

> use testDB
switched to db testDB

...

> db.createCollection("cappedCollection", {capped: true, size:10000})
{ "ok" : 1 }

...

> db.cappedCollection.insertOne({x:1})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605e919b9829a6a6b3fde5f5")
}

...

> db.cappedCollection.find()
{ "_id" : ObjectId("605e919b9829a6a6b3fde5f5"), "x" : 1 }


# maxSize 값이 10240 으로 설정된 것을 확인 가능하다. 위에서 입력한 db.createCollection() 메서드의 인자로 전달한 size = 10000 보다 크면서 가장 가까운 256의 배수인 10240 이 최대값으로 설정되었다.
> db.cappedCollection.stats()
{
	"ns" : "testDB.cappedCollection",
	"size" : 33,
	"count" : 1,
	"avgObjSize" : 33,
	"storageSize" : 16384,
	"capped" : true,
	"max" : -1,
	"maxSize" : 10240,
	"sleepCount" : 0,
	"sleepMS" : 0,
	"wiredTiger" : {
		"metadata" : {

...


> for(i=0; i<1000; i++){
... db.cappedCollection.insertOne({x:i})
... }
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605e93009829a6a6b3fde9dd")
}


...

> db.cappedCollection.find()
{ "_id" : ObjectId("605e93009829a6a6b3fde8a8"), "x" : 690 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8a9"), "x" : 691 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8aa"), "x" : 692 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8ab"), "x" : 693 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8ac"), "x" : 694 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8ad"), "x" : 695 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8ae"), "x" : 696 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8af"), "x" : 697 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b0"), "x" : 698 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b1"), "x" : 699 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b2"), "x" : 700 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b3"), "x" : 701 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b4"), "x" : 702 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b5"), "x" : 703 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b6"), "x" : 704 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b7"), "x" : 705 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b8"), "x" : 706 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8b9"), "x" : 707 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8ba"), "x" : 708 }
{ "_id" : ObjectId("605e93009829a6a6b3fde8bb"), "x" : 709 }
Type "it" for more
>



// 1000 개를 넘어섰고 10240 을 넘을 수도 있을 것 같은데 size가 10230 으로 제한된 것을 볼 수 있다.
> db.cappedCollection.stats()
{
	"ns" : "testDB.cappedCollection",
	"size" : 10230,
	"count" : 310,
	"avgObjSize" : 33,
	"storageSize" : 32768,
	"capped" : true,
	"max" : -1,
	"maxSize" : 10240,
	"sleepCount" : 0,
	"sleepMS" : 0,
	"wiredTiger" : {
...
```



# 3. 도큐먼트 생성



## 도큐먼트 생성 명령어 형식



### 파라미터

- document 
  - 입력할 도큐먼트를 입력
- WriteConcern
  - 선택적. WriteConcern 을 사용하려면 이 파라미터를 사용
- ObjectId
  - 생성된 도큐먼트의 ObjectId 값을 리턴



## 예제

```javascript
> use sampleData
switched to db sampleData

...

> db.user.insertOne({username: "sgjung", password: "1111"})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605e9fe99829a6a6b3fde9de")
}

...

> db.user.insertOne({username: "hello", password: "1111"})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605ea07a9829a6a6b3fde9df")
}

> db.sampleData.insertOne({name:'highway', number:1})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605ea14b9829a6a6b3fde9e0")
}

> db.sampleData.find()
{ "_id" : ObjectId("605ea14b9829a6a6b3fde9e0"), "name" : "highway", "number" : 1 }

...
id 를 따로 입력하여 생성하고자할때 아래와 같이 입력할 수 있다.
> db.sampleData.insertOne({
... _id: 200,
... name:'red zepplin',
... number: 2
... })
{ "acknowledged" : true, "insertedId" : 200 }
>
>
> db.sampleData.find().pretty()
{
	"_id" : ObjectId("605ea14b9829a6a6b3fde9e0"),
	"name" : "highway",
	"number" : 1
}
{
	"_id" : ObjectId("605ea9f89829a6a6b3fde9e1"),
	"name" : "highway",
	"number" : 1
}
{ "_id" : 200, "name" : "red zepplin", "number" : 2 }
```



## WriteConcern

insertOne 과 같은 명령어로 도큐먼트를 컬렉션에 insert 하려고 할 때, 이 컬렉션이 이미 쓰기(write - 도큐먼트 생성,수정,삭제) 작업을 하고 있는 상태일때 어떤 동작을 하도록 할지 설정하는 옵션이다.  



몽고 DB는 이런 쓰기(write - 도큐먼트 생성,수정,삭제)작업을 모두 마치고 결과를 반환해도 실제로 장기 저장장치에 바로 데이터를 저장하지 않고 메모리에 미리 저장해둔다. 그리고 이렇게 메모리에 저장해 둔 데이터를 장기 저장장치로 옮긴다.  



그런데 이렇게 메모리에 저장해두고 있는 상태에서 데이터베이스에 문제가 생기면, 메모리에만 담겨있는 데이터가 손실된다. WriteConcern 에 지정하는 값은 이러한 데이터 손실이 일어날 수 있는 최악의 상황을 어느정도 수준으로 막을지 결정할 수 있도록 돕는다. (수준을 높이면 높일수록 성능문제가 있고 등등 이런 내용에 대해서는 다른 문서에서 정리)   



의도치 않은 장애 상황을 대비해 미러 서버를 두기도 하고, 레플리카셋을 여러게 구성하여 이것들을 또 클러스터링하여서 하나의 묶음으로 생성할 수 있기도 하다.  



## insertOne(document)

```javascript
> use sampleData
switched to db sampleData

...

> db.user.insertOne({username: "hello", password: "1111"})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605ea07a9829a6a6b3fde9df")
}

> db.sampleData.insertOne({name:'highway', number:1})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605ea14b9829a6a6b3fde9e0")
}
```



## insertMany([... document ...])

```javascript
> use manyInsertTest
switched to db manyInsertTest

...

> db.manyInsertTest.insertMany(
... [{name: 'sgjung', menu: 'Americano'}, {name: 'Anna', menu: 'Milktea'}]
... )
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("605eaf5f9829a6a6b3fde9e2"),
		ObjectId("605eaf5f9829a6a6b3fde9e3")
	]
}

...

> db.manyInsertTest.find().pretty()
{
	"_id" : ObjectId("605eaf5f9829a6a6b3fde9e2"),
	"name" : "sgjung",
	"menu" : "Americano"
}
{
	"_id" : ObjectId("605eaf5f9829a6a6b3fde9e3"),
	"name" : "Anna",
	"menu" : "Milktea"
}
```



## BulkInsertError 란?

아래와 같이 일부러 `_id` 필드가 중복되도록 insert 되도록 해보자.

```javascript
> db.manyInsertTest.insertMany(
... [{_id: 200, name: 'Americano', price: 3000}, {_id: 200, name: 'MilkTea', price: 3000}]
... )
```



이렇게 하면 아래와 같이 `BulkInsertError` 가 발생하게 된다.  


## 원자성의 이해

MongoDB는 생성, 읽기, 수정, 삭제 작업에 대해 '원자성'을 확보하고 있다. 이건 나중에 정리하자... ㅠㅜ













