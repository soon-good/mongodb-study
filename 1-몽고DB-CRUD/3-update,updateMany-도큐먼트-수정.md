# 몽고DB - find,프로젝션,커서

<img src="/Users/kyle.sgjung/workspace/sgjung/mongodb-study/1-몽고DB-CRUD/img/mongo-icon-official-3.png" alt="이미지" style="zoom:25%;" />



# 참고자료

[맛있는 MongoDB, 비제이퍼블릭, 정승호 저](http://www.yes24.com/Product/Goods/85011885)  

# replaceOne() - 단일 도큐먼트 교체

## Overview

replaceOne() 명령어는 전달되는 query 인자값에 맞는 도큐먼트를 찾아서 교체하는 명령어이다.  

- query 로 조회한 도큐먼트를 replacement 파라미터로 도큐먼트를 교체한다.
- 수정이 아니라 교체이기 때문에 기존의 내용이 전부 사라진다는 특징을 가진다.  

  

## replaceOne() 메서드 형식

```javascript
db.collection명.replaceOne(
  <query>,
  <replacement>,
  {
  	upsert: <boolean>,
  	writeConcern: <document>,
  	collation: <document>
  }
)
```



## replaceOne() 파라미터들

- query 
  - document 타입
  - 업데이트할 도큐먼트 들을 추려낼 쿼리
- replacement
  - document 타입
  - 도큐먼트를 대체할 구문
- upsert
  - 선택적 파라미터
  - boolean 타입
  - upsert 값이 true 일 경우 쿼리한 도큐먼트가 없을 경우 새로운 도큐먼트를 추가한다.
- WriteConcern
  - 선택적 파라미터
  - document 타입
  - WriteConcern 전략을 전달해준다.
- collation
  - 선택적 파라미터
  - document 타입
  - 언어에 대한 설정을 지정한다. 악센트, 대소문자 관계에 대한 순서를 다룸



## 예제

**1) 수원 카페 조회(이전에 입력한 데이터이다.)**

```javascript
> db.suwon_cafe.find()
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0d"), "name" : "Americano", "price" : 1000, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0e"), "name" : "Milk tea", "price" : 2000, "minute" : 4 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0f"), "name" : "CaffeLatte", "price" : 2000, "minute" : 4 }
```



**2) 수원 카페 데이터에서 밀크티 항목의 이름, 가격, 대기시간 데이터를 수정하여 replace 해보기**

```javascript
> db.suwon_cafe.replaceOne(
  // replace 할 데이터 조회 쿼리
... { name : "Milk tea" },
  
  // replace 할 데이터 본문
... {
...   name : "Milk Tea",
...   price : 1600,
...   minute: 3
... }
  
... )

  
...

{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```



**3) 수원 카페 데이터에서 밀크티 항목의 데이터를 확인해보기**

```javascript
> db.suwon_cafe.find()
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0d"), "name" : "Americano", "price" : 1000, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0e"), "name" : "Milk Tea", "price" : 1600, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0f"), "name" : "CaffeLatte", "price" : 2000, "minute" : 4 }
```



**4) upsert 지정하여 데이터가 없을 경우는 insert 하는 예제**

upsert 전 데이터

```javascript
> db.suwon_cafe.find()

{ "_id" : ObjectId("605eb7dc69ec4e48c406de0d"), "name" : "Americano", "price" : 1000, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0e"), "name" : "Milk Tea", "price" : 1600, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0f"), "name" : "CaffeLatte", "price" : 2000, "minute" : 4 }
>
```



upsert 쿼리 수행

```javascript
> db.suwon_cafe.replaceOne(
  // replace 할 데이터 조회 쿼리
... { name : "마끼아또" },

  // replace 할 데이터 본문
  // upsert body 정의. 데이터가 없을 경우 insert 진행
... {
...   name : "마끼아또",
...   price : 2500,
...   minute: 4
... },
  
  // upsert 여부 지정
... { upsert : true }
... )

// 출력결과
{
	"acknowledged" : true,
	"matchedCount" : 0,
	"modifiedCount" : 0,
	"upsertedId" : ObjectId("605f095a8614e26119136ef1")
}
```



출력결과 확인

```javascript
> db.suwon_cafe.find()
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0d"), "name" : "Americano", "price" : 1000, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0e"), "name" : "Milk Tea", "price" : 1600, "minute" : 3 }
{ "_id" : ObjectId("605eb7dc69ec4e48c406de0f"), "name" : "CaffeLatte", "price" : 2000, "minute" : 4 }
{ "_id" : ObjectId("605f095a8614e26119136ef1"), "name" : "마끼아또", "price" : 2500, "minute" : 4 }
```



# updateOne(), updateMany() - 도큐먼트 수정

## Overview

replace 의 경우 바디를 그대로 지정하면 되었지만, update 구문은 별도의 연산자가 필요하다. 

- $set
- $unset
- $currentDate
- $inc
- $min, $max, 
- $mul
- $rename
- $setOnInsert

  

## updateOne() 메서드 형식

```javascript
db.collection명.updateOne(
  <query>,
  <update>,
  {
  	upsert: <boolean>,
  	writeConcern: <document>,
  	collation: <document>,
  	arrayFilters: [ <filteredDocument1>, ... ]
  }
)
```



## updateMany() 메서드 형식

```javascript
db.collection명.updateMany(
  <query>,
  <update>,
  {
  	upsert: <boolean>,
  	writeConcern: <document>,
  	collation: <document>,
  	arrayFilters: [<filteredDocument1>, ... ]
  }
)
```



## 파라미터들

- query 
  - document 타입
  - 업데이트할 도큐먼트 들을 추려낼 쿼리
- update
  - document 타입
  - 업데이트 목적의 도큐먼트 바디
- upsert
  - 선택적 파라미터
  - boolean 타입
  - upsert 값이 true 일 경우 쿼리한 도큐먼트가 없을 경우 새로운 도큐먼트를 추가한다.
- WriteConcern
  - 선택적 파라미터
  - document 타입
  - WriteConcern 전략을 전달해준다.
- collation
  - 선택적 파라미터
  - document 타입
  - 언어에 대한 설정을 지정한다. 악센트, 대소문자 관계에 대한 순서를 다룸

- arrayFilters
  - 선택적 파라미터
  - Array 타입
  - 지정된 배열 필드에서 업데이트 작업을 위해 수정할 배열요소를 결정하는 필터문서 배열



## update 연산자

update 구문의 경우 단순히 파라미터를 지정하는 것만으로 가능한 것이 아니고, 연산자를 사용해야 한다. 만약 update(), updateMany() 함수 내에 replace() 메서드 의 예처럼 쿼리만을 사용한다면 익셉션이 발생한다. 쿼리 본문에 $set, $inc 등과 같은 update(), updateMany() 등에 필요한 연산자를 지정해주어야 한다.  


- $set
- $unset
- $currentDate
- $inc
- $min, $max, 
  - 도큐먼트의 field 의 값이 \< 최댓값 \> 이면 \< 최댓값 \> 으로 설정한다.
  - 도큐먼트의 field 의 값이 \< 최솟값 \> 이면 \< 최솟값 \> 으로 설정한다.
- $mul
  - 도큐먼트의 field 의 값을 \< 배수 \> 를 곱한 값으로 수정
- $rename
  - 도큐먼트의 field 의 값을 \< 새로운이름 \> 으로 수정
- $setOnInsert



## 예제 - updateMany

**1) 예제 데이터 생성하기**  

```javascript
> db.employee.insertOne({ name: "소방관 #1", job: "fire fighter", salary: 4000})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605f148e69ec4e48c406e1fa")
}
> db.employee.insertOne({ name: "소방관 #2", job: "fire fighter", salary: 4000})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605f149269ec4e48c406e1fb")
}
> db.employee.insertOne({ name: "소방관 #3", job: "fire fighter", salary: 4000})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605f149669ec4e48c406e1fc")
}
> db.employee.insertOne({ name: "선생님 #1", job: "Teacher", salary: 500})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605f149d69ec4e48c406e1fd")
}
> db.employee.insertOne({ name: "선생님 #2", job: "Teacher", salary: 500})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("605f14a269ec4e48c406e1fe")
}
```



**2) insert 한 데이터들 확인해보기**  

```javascript
> db.employee.find()
{ "_id" : ObjectId("605f148e69ec4e48c406e1fa"), "name" : "소방관 #1", "job" : "fire fighter", "salary" : 4000 }
{ "_id" : ObjectId("605f149269ec4e48c406e1fb"), "name" : "소방관 #2", "job" : "fire fighter", "salary" : 4000 }
{ "_id" : ObjectId("605f149669ec4e48c406e1fc"), "name" : "소방관 #3", "job" : "fire fighter", "salary" : 4000 }
{ "_id" : ObjectId("605f149d69ec4e48c406e1fd"), "name" : "선생님 #1", "job" : "Teacher", "salary" : 500 }
{ "_id" : ObjectId("605f14a269ec4e48c406e1fe"), "name" : "선생님 #2", "job" : "Teacher", "salary" : 500 }
```



**3) updateMany 수행하기**  

```javascript
> db.employee.updateMany(
... { job : "fire fighter" },
... { $set : { job : "Fire Fighter", salary : 9000 } }
... )
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }
>
```

  

**4) 쿼리 수행결과 확인**

> 소방관 #1 ~ 3 의 job 이 모두 "**fire fighter**" 에서 "**Fire Fighter**" 로 변경된것을 확인 가능하다.

```javascript
> db.employee.find()
{ "_id" : ObjectId("605f148e69ec4e48c406e1fa"), "name" : "소방관 #1", "job" : "Fire Fighter", "salary" : 9000 }
{ "_id" : ObjectId("605f149269ec4e48c406e1fb"), "name" : "소방관 #2", "job" : "Fire Fighter", "salary" : 9000 }
{ "_id" : ObjectId("605f149669ec4e48c406e1fc"), "name" : "소방관 #3", "job" : "Fire Fighter", "salary" : 9000 }
{ "_id" : ObjectId("605f149d69ec4e48c406e1fd"), "name" : "선생님 #1", "job" : "Teacher", "salary" : 500 }
{ "_id" : ObjectId("605f14a269ec4e48c406e1fe"), "name" : "선생님 #2", "job" : "Teacher", "salary" : 500 }
```



## 예제 - arrayFilters 활용

배열데이터를 filter 옵션을 걸어서 수정하는 예제를 확인해보자.

**1) 샘플 데이터 Insert**

```javascript
db.developers.insertOne({ name: "Kyle", skill_set: ["java", "javascript", "sql", "kotlin"], job: "developer" })
db.developers.insertOne({ name: "John", skill_set: ["java", "sql"], job: "developer" })
db.developers.insertOne({ name: "Marie", skill_set: ["javascript", "css"], job: "developer" })
```



**2) Insert 된 데이터 확인**

```javascript
> db.developers.find()
{ "_id" : ObjectId("605f18de69ec4e48c406e1ff"), "name" : "Kyle", "skill_set" : [ "java", "javascript", "sql", "kotlin" ], "job" : "developer" }
{ "_id" : ObjectId("605f18e469ec4e48c406e200"), "name" : "John", "skill_set" : [ "java", "sql" ], "job" : "developer" }
{ "_id" : ObjectId("605f18ea69ec4e48c406e201"), "name" : "Marie", "skill_set" : [ "javascript", "css" ], "job" : "developer" }
```



**3) skill_set 내에 "java" 가 있으면 "Java"로 치환하기**

```javascript
> db.developers.updateMany(
... { },
... {
...   $set: { "skill_set.$[javaElem]": "Java" }
... },
... {
...   arrayFilters: [{javaElem: "java"}]
... }
... )
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 2 }
```



**4) 업데이트 된 결과 확인**

```javascript
> db.developers.find()
{ "_id" : ObjectId("605f18de69ec4e48c406e1ff"), "name" : "Kyle", "skill_set" : [ "Java", "javascript", "sql", "kotlin" ], "job" : "developer" }
{ "_id" : ObjectId("605f18e469ec4e48c406e200"), "name" : "John", "skill_set" : [ "Java", "sql" ], "job" : "developer" }
{ "_id" : ObjectId("605f18ea69ec4e48c406e201"), "name" : "Marie", "skill_set" : [ "javascript", "css" ], "job" : "developer" }
```



# TODO

76 page 부터는 다시 정리하기...