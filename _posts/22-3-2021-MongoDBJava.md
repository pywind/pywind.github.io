---
layout: post
title: Java MongoDB Step by Step
date: 2020-08-28
project: true
excerpt: "Làm việc với MongoDB bằng JAVA"
tags: [MongoDB, Java, database]
comments: false
---

# MongoDB Step by Step

> Language: Java

## MongoDB

Một số khái niệm: 

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

**Cấu hình pom.xml**

Ví dụ: 

```jv
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

## Tạo những cấu trúc được định hình sẵn

```jv
public class MyConstants {
 
  // Nếu tên DB này chưa có trong hệ thống
  // Khi được gọi, nó sẽ được tự động tạo ra
  public static final String DB_NAME ="MyStudyDB";
 
}
```

### Cấu hình giao tiếp

> Mọi kết nối đều cần thông qua HOST:PORT tương ứng, vì vậy chúng ta cần khai báo chúng để sử dụng và thay đổi(có thể)

1. Khai báo host và port
```jv
// Host: địa chỉ của host, ở đây là cá nhân nên sẽ là localhost
private static final String HOST = "localhost";
// port: Cổng tương ứng
private static final int PORT = 27017;
```

2. Kết nối qua Client

Để kết nối vào các host bằng MongoDB, ta cần các client. MongoClient là tuyến đường của bạn ở MongoDB, từ điều này, bạn sẽ nhận được database -> collection để làm việc

```jv
MongoClient mongoClient = new MongoClient();
```

Một cách đơn giản nếu bạn đang có một kết nối local và sử dụng default port.

Hoặc: 

```jv
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

```jv
//Tạo chứng chỉ  
MongoCredential credential = MongoCredential.createMongoCRCredential(
              USERNAME, MyConstants.DB_NAME, PASSWORD.toCharArray());
// Tạo client truy cập, tuy nhiên thêm chứng chỉ
      MongoClient mongoClient = new MongoClient(
              new ServerAddress(HOST, PORT), Arrays.asList(credential));
```

4. Kết nối vào database

**Java** cung cấp Object DB để truy vấn database, hãy đảm bảo bạn đã tạo mongoClient mà không bị dính *UnknownHostException*.

```jv 
// (Không nhất thiết DB này phải tồn tại sẵn
// nó sẽ được tự động tạo ra nếu chưa tồn tại).
DB db = mongoClient.getDB(MyConstants.DB_NAME);
```

Khi đã có quyền tới database, bây giờ cùng đến với khái niệm **Collection**

```jv
// Ví dụ lấy ra Collection với tên Department.
// Không nhất thiết Collection này phải tồn tại trong DB.
DBCollection dept = db.getCollection("Department");
```

Source code ví dụ: 

```jv
package org.o7planning.tutorial.MongoDB;
 
import java.net.UnknownHostException;
import java.util.Arrays;
import java.util.List;
 
import com.MongoDB.MongoClient;
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

```jv
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

```
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

1. Các operators

|  $gt  | Lớn hơn giá trị cần truy vấn. (Greater than)  |
| :---: | :---: | 
|  $gte  |  Lớn hơn hoặc bằng giá trị cần truy vấn. (Greater than or Equals)  |
| $in | Khớp với bất kỳ giá trị nào nằm trong một tập hợp cho trước. (In) | 
| $lt | Nhỏ hơn giá trị quy định trong truy vấn. (Less than)|
|$lte | Nhỏ hơn hoặc bằng giá trị quy định trong truy vấn. (Less than or Equals)|
|$ne| Khác với giá trị quy định trong truy vấn. (Not Equals)|
|$nin| Không khớp với bất kỳ giá trị nào nằm trong một tập hợp cho trước. (Not in) |

2. Java API

```jv
// Một số method của class DBCollection cho việc truy vấn:
public DBCursor find(DBObject ref)
  
public DBCursor find(DBObject ref, DBObject keys)

```
