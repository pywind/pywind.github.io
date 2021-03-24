---
layout: post
title: Java MongoDB Step by Step
date: 2020-08-28
project: true
excerpt: "Làm việc với MongoDB bằng Java"
tags: [MongoDB, Java, database]
comments: false
---

# MongoDB Step by Step

> Language: Java

## MongoDB

### Một số khái niệm

* Collection: Tương ứng Table hay relation trong các CSDL khác
* Document: Tương ứng với record, row trong các CSDL khác

`MongoDB rất linh hoạt, Bạn kết nối vào một Database hoặc Collection mà không nhất thiết rằng nó tồn tại, nó sẽ được tự động tạo ra khi bạn có một thao tác nào đó.`

> MongoDB không có khái niệm *Join* (kết nối) giữa 2 Collection với nhau, mọi việc truy vấn đều thực hiện trên một Collection, mọi thứ chỉ cần **where** cho một **collection**.

**Java** cung cấp lớp DBObject để mô tả dữ liệu dạng JSON

## Tạo một project

**Maven project** được sử dụng để tương tác với MongoDB thông qua ngôn ngữ JAVA, IDE cụ thể là ECLIPSE

Nhập vào: 

* Group id:
* Artifact id

### Cấu hình Maven

#### **Cấu hình pom.xml**

Ví dụ:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
 <modelVersion>4.0.0</modelVersion>
 <groupId>#group</groupId>
 <artifactId>#JavaMongoDBTutorial</artifactId>
 <version>0.0.1-SNAPSHOT</version>
 
 <dependencies>
    
     <!--  http://mvnrepository.com/artifact/org.MongoDB/mongo-java-driver  -->
     <dependency>
         <groupId>org.MongoDB</groupId>
         <artifactId>mongo-java-driver</artifactId>
         <version>2.12.4</version>
     </dependency>
    
 </dependencies>
 
</project>
```

## Tạo những phương thức định hình

```java
public class MyConstants {
 
  // Nếu tên DB này chưa có trong hệ thống
  // Khi được gọi, nó sẽ được tự động tạo ra
  public static final String DB_NAME ="MyStudyDB";
 
}
```

### Cấu hình giao tiếp

> Mọi kết nối đều cần thông qua HOST:PORT tương ứng, vì vậy chúng ta cần khai báo chúng để sử dụng và thay đổi(có thể)

1. Khai báo host và port

```java
// Host: địa chỉ của host, ở đây là cá nhân nên sẽ là localhost
private static final String HOST = "localhost";
// port: Cổng tương ứng
private static final int PORT = 27017;
```

2. Kết nối qua Client

Để kết nối vào các host bằng MongoDB, ta cần các client. MongoClient là tuyến đường của bạn ở MongoDB, từ điều này, bạn sẽ nhận được database -> collection để làm việc

```java
MongoClient mongoClient = new MongoClient();
```

Một cách đơn giản nếu bạn đang có một kết nối local và sử dụng default port.

Hoặc:

```java
MongoClient mongoClient = new MongoClient(new MongoClientURI("MongoDB://"Host":'port'"));
// Sử dụng URI để kết nối nhanh chóng
// Hoặc dễ hiểu hơn
MongoClient mongoClient = new MongoClient(HOST, PORT);
```

**Lưu ý**: Thực hiện kết nối sẽ ném ra Exception có thể kiểm tra, *UnknownHostException*.  Bạn sẽ phải nắm bắt điều này hoặc đơn giản  khai báo nó, tùy thuộc vào cách của bạn là gì để xử lý ngoại lệ. Đơn giản là *Throws*.

3. Chứng chỉ kết nối

> Cách kết vào MongoDB có 2 loại là có bảo mật và không bảo mật

* Không bảo mật: Đơn giản là chỉ cần làm như (2)
* Có bảo mật: bạn phải có credential bằng user và password để chúng minh bản thân đang sở hữu quyền truy cập

```java
//Tạo chứng chỉ  
MongoCredential credential = MongoCredential.createMongoCRCredential(
              USERNAME, MyConstants.DB_NAME, PASSWORD.toCharArray());
// Tạo client truy cập, tuy nhiên thêm chứng chỉ
      MongoClient mongoClient = new MongoClient(
              new ServerAddress(HOST, PORT), Arrays.asList(credential));
```

4. Kết nối vào database

**Java** cung cấp Object DB để truy vấn database, hãy đảm bảo bạn đã tạo mongoClient mà không bị dính *UnknownHostException*.

```java
// (Không nhất thiết DB này phải tồn tại sẵn
// nó sẽ được tự động tạo ra nếu chưa tồn tại).
DB db = mongoClient.getDB(MyConstants.DB_NAME);
```

Khi đã có quyền tới database, bây giờ cùng đến với khái niệm **Collection**

```java
// Ví dụ lấy ra Collection với tên Department.
// Không nhất thiết Collection này phải tồn tại trong DB.
DBCollection dept = db.getCollection("Department");
```

Source code ví dụ:

```java
package org.o7planning.tutorial.mongodb;
 
import java.net.UnknownHostException;
import java.util.Arrays;
import java.util.List;
 
import com.mongodb.MongoClient;
import com.MongoDB.MongoCredential;
import com.MongoDB.ServerAddress;
 
public class MongoUtils {
 
  private static final String HOST = "localhost";
  private static final int PORT = 27017;
 
  //
  private static final String USERNAME = "mgdb";
  private static final String PASSWORD = "1234";
 
  // Cách kết nối vào MongoDB không bắt buộc bảo mật.
  private static MongoClient getMongoClient_1() throws UnknownHostException {
      MongoClient mongoClient = new MongoClient(HOST, PORT);
      return mongoClient;
  }
 
  // Cách kết nối vào DB MongoDB có bảo mật.
  private static MongoClient getMongoClient_2() throws UnknownHostException {
      MongoCredential credential = MongoCredential.createMongoCRCredential(
              USERNAME, MyConstants.DB_NAME, PASSWORD.toCharArray());
 
      MongoClient mongoClient = new MongoClient(
              new ServerAddress(HOST, PORT), Arrays.asList(credential));
      return mongoClient;
  }
 
  public static MongoClient getMongoClient() throws UnknownHostException {
      // Kết nối vào MongoDB không bắt buộc bảo mật.
      return getMongoClient_1();
      // Bạn có thể thay thế bởi getMongoClient_2()
      // trong trường hợp kết nối vào MongoDB có bảo mật.
  }
 
  private static void ping() throws UnknownHostException {
      MongoClient mongoClient = getMongoClient();
      
      System.out.println("List all DB:");
      
      // Danh sách các DB Names.
      List<String> dbNames = mongoClient.getDatabaseNames();
      for (String dbName : dbNames) {
          System.out.println("+ DB Name: " + dbName);
      }
 
      System.out.println("Done!");
  }
 
  // Test
  public static void main(String[] args) throws UnknownHostException {
      ping();
  }
}
```

### Insert

> Đảm bảo cài đặt cấu hình đã hoàn tất, *MongoDB rất linh hoạt, nếu bạn muốn trèn một bản ghi (Document) vào một Collection. Nếu Collection này không tồn tại nó sẽ tự động được tạo ra, sau đó các bản ghi được trèn vào.*
> Primary trong collection luôn là **_id**, trong trường hợp bạn trèn vào **Document**, mà không chỉ định rõ khóa chính, cột **_id** vẫn sẽ được tạo ra, với giá trị là một chuỗi ngẫu nhiên 36 ký tự.

Khi đã có Collection bằng *getCollection*, chỉ đơn giản là tạo các document và insert vào collection.

```java
List<Integer> books = Arrays.asList(27464, 747854);
DBObject person = new BasicDBObject("_id", "jo")
                            .append("name", "Jo Bloggs")
                            .append("address", new BasicDBObject("street", "123 Fake St")
                                                         .append("city", "Faketon")
                                                         .append("state", "MA")
                                                         .append("zip", 12345))
                            .append("books", books);

dept.insert(person)
```

Trong .JSON nó sẽ như này:

```json
person = {
  _id: "jo",
  name: "Jo Bloggs",
  age: 34,
  address: {
    street: "123 Fake St",
    city: "Faketon",
    state: "MA",
    zip: &#x201C;12345&#x201D;
  }
  books: [ 27464, 747854, ...]
}  
```

`Note`: Trong một collection, không nhất thiết các document phải cùng chung các properties.

## Truy vấn trong MongoDB

### Các operators

|Operand| Ý nghĩa|
| :---: | :---: |
|  $gt  | Lớn hơn giá trị cần truy vấn. (Greater than)  |
|  $gte  |  Lớn hơn hoặc bằng giá trị cần truy vấn. (Greater than or Equals)  |
| $in | Khớp với bất kỳ giá trị nào nằm trong một tập hợp cho trước. (In) |
| $lt | Nhỏ hơn giá trị quy định trong truy vấn. (Less than)|
|$lte | Nhỏ hơn hoặc bằng giá trị quy định trong truy vấn. (Less than or Equals)|
|$ne| Khác với giá trị quy định trong truy vấn. (Not Equals)|
|$nin| Không khớp với bất kỳ giá trị nào nằm trong một tập hợp cho trước. (Not in) |

### Java API

```java
// Một số method của class DBCollection cho việc truy vấn:
public DBCursor find(DBObject ref)
  
public DBCursor find(DBObject ref, DBObject keys)

```

### Query có điều kiện đơn giản

Sử dụng DBCursor như một con trỏ vào các document

* .hasNext() -> boolean: Trả về nếu đã trỏ đến Document cuối cùng
* .next() -> Document: Trỏ đến Document tiếp theo

`Quy trình`: tạo ra các đối tượng **DBObject** bạn có thể sử dụng **BasicDBObjectBuilder**

```java
BasicDBObjectBuilder whereBuilder = BasicDBObjectBuilder.start();
whereBuilder.append("dept_name", "ACCOUNTING");
//
DBObject where  = whereBuilder.get();
        
// Query
DBCursor cursor = dept.find(where);
```

* Tạo **BasicDBObjectBuilder** với phương thức start() 'create an empty obj' -> .append(ref, keys) -> build DBObject thông qua .get() -> Tạo DBCursor bằng cách sử dụng coll.find(DBObject where) -> Cursor này sẽ trỏ đến phần tử đầu tiên trong collection -> sử dụng .hasNext() & .next() để trỏ đến các document tiếp theo

### Query có điều kiện giữa add & append

* *.push*(keys) : tạo một empty Object mới và chèn nó vào Object hiện tại bằng key đã cho. Object con mới sẽ trở thành đối tượng hoạt động.
* *.apppend*(String key, Obj val): thêm khóa : giá trị vào Object hoạt động
* *.add() = .append()*
* *.pop()* : bật Object hoạt động, có nghĩa là Object mẹ trở nên hoạt động
* *.get()* : lấy base object

#### Query kết hợp với RegEx

```java
public static DBObject getWhereClause() {
 
      BasicDBObjectBuilder whereBuilder = BasicDBObjectBuilder.start();
      
      // Sử dụng append và add là giống nhau.
      whereBuilder.push("dept_name").add("$regex", ".*A.*") ;
      whereBuilder.pop();
      whereBuilder.append("description", null);
      //
      DBObject where = whereBuilder.get();
      System.out.println("Where " + where.toString());
      return where;
  }
```

* Giải thích:

<figure>
 <a href="https://s1.o7planning.com/vi/10289/images/85966.png"><img src="https://s1.o7planning.com/vi/10289/images/85966.png"></a>
 <figcaption> Create a query </figcaption>
</figure>

* Giải thích: obj:key
* push: Thêm điều kiện "Dept_name" bằng : key bỏ vào query
* add("$regex", ".*A.*"):  thêm key(có thể nhiều)
  * "$regex": Tìm trên mọi dòng
  * ".*A.*":
    * `.` : khớp với bất kỳ ký tự nào (ngoại trừ ký tự kết thúc dòng)
    * `*` : Khớp với mọi ký tự, có thể lặp lại nhiều lần
    * `A` : khớp với ký tự A (phân biệt chữ hoa chữ thường)
* .pop(): ra khỏi dòng truy vấn của obj "Dept_name"
* .append("description", null): thêm điều kiện "description" = null

#### Hoặc đơn giản hơn, sử dụng regex để complie

```java
public static DBObject getWhereClause() {
 
    BasicDBObjectBuilder whereBuilder = BasicDBObjectBuilder.start();
 
    // Biểu thức chính quy mô tả một chuỗi.
    // Bắt đầu bởi ký tự bất kỳ xuất hiện 0 hoặc nhiều lần
    // Tiếp theo là ký tự S.
    // Và tiếp theo là ký tự xuất hiện 0 hoặc nhiều lần.
    String regex = ".*S.*";
    Pattern pattern = Pattern.compile(regex);
 
    whereBuilder.append("dept_name", pattern);
    whereBuilder.append("description", null);
    //
    DBObject where = whereBuilder.get();
    System.out.println("Where: " + where.toString());
    return where;
  }

```

### Query sử dụng **QueryBuilder**

```java
// Viết điều kiện where sử dụng QueryBuilder.
// Sử dụng QueryBuilder luôn luôn dễ dàng hơn.
// dept_name in ('ACCOUNTING', 'RESEARCH') or location  = 'BOSTON'.
// { "$or" : [ { "dept_name" : { "$in" : [ "ACCOUNTING" , "RESEARCH"]}} , {"location" : "BOSTON"}]}
  protected static DBObject getWhereClause() {
      List<String> list = new ArrayList<String>();
      list.add("ACCOUNTING");
      list.add("RESEARCH");
      //
      QueryBuilder qb1 = new QueryBuilder();
      qb1.put("dept_name").in(list);
      DBObject q1 = qb1.get();
      //
      QueryBuilder qb2 = new QueryBuilder();
      qb2.put("location").("BOSTON");
      DBObject q2 = qb2.get();
 
       //
      QueryBuilder queryBuilder = QueryBuilder.start();
      queryBuilder.or(q1, q2);
 
      DBObject query = queryBuilder.get();
      return query;
  }
 
```

Giải thích:

* QueryBuilder: Tiện ích cho việc tạo ra các truy vấn DBObject
* .put(string key): Thêm khóa mới vào truy vấn nếu chưa có. Đặt khóa này làm khóa hiện tại.
* .in(Object obj) = Tương đương $in operand
* .is(Object obj) = Tương đương find({key:value})  
* Công thức chung: Luôn build từ điều kiện trong trước
  * Tạo list chứa 2 value cần tìm trong "dept_name"
  * Tạo qb1 với .put("dept_name") và toán hạng $in bằng dùng hàm .in
  * Tạo qb2 với .put("location") và dùng .is() để build cũng như cách dùng hàm append(key, value) ở các bên trên
  * Build query cuối dùng $or bằng hàm or sau đó .get() để tạo DBObject cho driver có thể tìm kiếm

#### Cách ngắn gọn hơn

```java
List<String> list = new ArrayList<String>();
       list.add("ACCOUNTING");
       list.add("RESEARCH");
       //
       QueryBuilder queryBuilder = QueryBuilder.start();
       queryBuilder.or(QueryBuilder.start().put("dept_name").in(list).get(),
               QueryBuilder.start().put("location").is("BOSTON").get());
 
       DBObject query = queryBuilder.get();
       System.out.println("Query = " + query);
       return query;
```

Kết quả ta vẫn thu được đoạn query như trên :

```json
{ "$or" : [ { "dept_name" : { "$in" : [ "ACCOUNTING" , "RESEARCH"]}} , {"location" : "BOSTON"}]}
```

## Update Document

### Update bằng cách Replace

```java
      DBObject whereClause = new BasicDBObject("city_no", "WAS");
 
      DBObject values = new BasicDBObject();
      values.put("population", 1200000);
      values.put("description", "Pop 2014");
      values.put("note", "Document replaced!");
 
      // Thực hiện việc update.
      WriteResult result = city.update(whereClause, values);
      int effectedCount = result.getN();
      System.out.println("result: " + values.toString());
      System.out.println("Effected Count: " + effectedCount);
```

Quy trình:

* Build DBObject để tìm Document cần update
  * Có thể dùng Query hoặc BasicDBObject
  
* Tạo DBObject value để update: Sử dụng các hàm .put, .append,...

* .update(Object cur, Object re): Sửa đổi một tài liệu hiện có hoặc các tài liệu trong bộ sưu tập. Theo mặc định, phương pháp này cập nhật một tài liệu duy nhất.


<figure>
 <a href="https://s1.o7planning.com/vi/10289/images/86217.png"><img src="https://s1.o7planning.com/vi/10289/images/86217.png"></a>
 <figcaption> Kết quả chạy ví dụ và so sánh document trước và sau khi update </figcaption>
</figure>

### Update với $set

<figure>
 <a href="https://s1.o7planning.com/vi/10289/images/86217.png"><img src="https://s1.o7planning.com/vi/10289/images/86235.png"></a>
 <figcaption> Thêm và sửa đổi đúng ! </figcaption>
</figure>

`Note`: Điểm khác so với cách update không có $set: bạn sẽ phải build thêm 1 DBObject chứa operand {$set :values}

```java
      DBCollection city = db.getCollection("City");
 
      // Tìm các City có city_no = "CHI".
      DBObject whereClause = new BasicDBObject("city_no", "CHI");
 
      DBObject values = new BasicDBObject();
      values.put("population", 3400000);
      values.put("description", "Pop 2014");
      values.put("note", "Document update with $set");
      
      DBObject valuesWithSet = new BasicDBObject();
      valuesWithSet.put("$set", values);
 
      // Thực hiện việc update.
      WriteResult result = city.update(whereClause, valuesWithSet);
      
      int effectedCount = result.getN();
      System.out.println("Effected Count: " + effectedCount);
      System.out.println("Done!");
```

<figure>
 <a href="https://s1.o7planning.com/vi/10289/images/86258.png"><img src="https://s1.o7planning.com/vi/10289/images/86258.png"></a>
 <figcaption> Kết quả sau khi update với set</figcaption>
</figure>

### Update với $inc

`Note`: Nếu cần thay đổi một cột nào đó bằng cách tăng thêm -> sử dụng $inc

```java
    DBCollection city = db.getCollection("City");
 
     // Tìm các City có city_no = "NYO".
     DBObject whereClause = new BasicDBObject("city_no", "NYO");
 
     DBObject values = new BasicDBObject();
     values.put("population", 10000);
    
    
     DBObject valuesWithInc = new BasicDBObject();
     valuesWithInc.put("$inc", values);
 
     // Thực hiện việc update.
     WriteResult result = city.update(whereClause, valuesWithInc);
    
     int effectedCount = result.getN();
     System.out.println("Effected Count: " + effectedCount);

```

