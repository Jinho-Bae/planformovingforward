Parquet
===

## Table of Contents

[TOC]

## Parquet(파케이)란?

* 데이터를 저장하는 방식(파일 포맷) 중 하나

하둡에 데이터를 저장한다는 것은 일반적으로 빅데이터를 저장한다는 이야기. 따라서 데이터를 효율적으로 저장하기 위해 다음 3가지 사항 필요

1. 빠른 읽기
2. 좋은 압축률
3. 특정 언어에 종속되지 않음

>  Hadoop 에코시스템 내 어떤 프로젝트에서도 이용가능한 **컬럼 기반의 저장 포맷**.
>  데이터처리 프레임워크, 데이터 모델, 프로그래밍 언어에 구애받지 않음

컬럼 기반의 저장 포맷
---
![](https://i.imgur.com/yi4039x.png)


행 기반 저장
![](https://i.imgur.com/IdTOc1l.png "row-oriented storage")

컬럼 기반 저장
![](https://i.imgur.com/Zhqwo9g.png "column-oriented storage")

컬럼 기반 저장 방식의 좋은 점 3가지

1. **압축률이 좋음**
유사한 데이터들이 모여 있기 때문
2. **I/O 사용률이 줄어듦**
데이터를 읽을 때 일부 컬럼만 스캔하기 때문에
3. **컬럼 별로 적합한 인코딩 사용 가능**
각 컬럼의 데이터들은 동일한 유형이기 때문에

## model
컬럼 기반 포맷으로 저장하기 위해서 먼저, 데이터 구조를 schema를 사용해 그려야 한다.  
Protocol buffers(구조화된 데이터를 직렬화하는 method)와 유사한 모델을 사용.  
* group을 사용하여 **중첩(nesting)**
* repeated field를 사용하여 **반복**

Map, List, Set과 같은 복잡한 타입은 필요 없다. 이들은 모두 repeated fields와 group의 조합으로 맵핑될 수 있기 때문에.

* schema의 root는 **message**라 불리우는 1개의 group
* 이 group의 각 **field**는 3개의 속성을 가진다.
    * repetition
        * required: exactly one occurrence
        * optional: 0 or 1 occurrence
        * repeated: 0 or more occurrences
    * type
        * group
        * primitive type (e.g., int, float, boolean, string)
    * name

schema의 예제 (AddressBook)
```
message AddressBook {
    required string owner;
    repeated string ownerPhoneNumbers;
    repeated group contacts {
        required string name;
        optional string phoneNumber;
    }
}
```
Lists (or Sets) can be represented by a repeating field.
![](https://i.imgur.com/vXQdzM7.png)

A Map is equivalent to a repeating field containing groups of key-value pairs where the key is required.
![](https://i.imgur.com/5QDiFOL.png)



## nested structure
nested data 구조를 컬럼 기반 format에 저장하기 위해서 스키마를 column list에 맵핑  
(레코드를 flat column에 쓰고, 그렇게 쓴 데이터를 원래 중첩 데이터 구조로 다시 읽을 수있는 방식으로)
![](https://i.imgur.com/PKyxboy.png)
파란색의 primitive type 당 1개의 컬럼을 생성
![](https://i.imgur.com/66oAlgb.png)
각 레코드는 두 개의 정수의 값을 갖고 있다. definition level, repetition level  
이 두 값을 사용하여 nested 구조를 완전히 복원할 수 있다.
* Definition Level  
root(0)부터 column의 가장 깊은 깊이까지.  
중첩된 구조를 위해서 null인 필드에 대한 level이 필요  
현재 depth에서 required가 **아닌** field 중 null이 아닌 것의 개수(?)
![](https://i.imgur.com/RqD7uK1.png)
![](https://i.imgur.com/wTJTgKx.png)

    아래와 같이 required field는 definition level이 필요치 않다.

```
message ExampleDefinitionLevel {
    optional group a {
        required group b {
            optional string c;
        }
    }
}
```
![](https://i.imgur.com/xAYlmwe.png)
* Repetition Level  
각 level에서 새로운 리스트의 시작 marker.  
현재 컬럼의 새로운 값을 추가하기 위해 필요
![](https://i.imgur.com/Y2yIgc6.png)
![](https://i.imgur.com/hoLhX5h.png)
    
    0은 모든 레벨에 대하여 새로운 데이터.  
    1은 level1에 대한 새로운 데이터.  
    2는 level2에 대한 새로운 데이터.
    optional과 required field는 반복하지 않기 때문에 repetition level 불필요

## nested structure 예제
```
AddressBook {
owner: "Julien Le Dem",
ownerPhoneNumbers: "555 123 4567",
ownerPhoneNumbers: "555 666 1337",
contacts: {
name: "Dmitriy Ryaboy",
phoneNumber: "555 987 6543",
},
contacts: {
name: "Chris Aniszczyk"
}
}
AddressBook {
owner: "A. Nonymous"
}
```
위 예제를 contacts.phoneNumber 기준으로 표현하면,
```
AddressBook {
contacts: {
phoneNumber: "555 987 6543"
}
contacts: {
}
}
AddressBook {
}
```
위 컬럼은 아래와 같이 표현
![](https://i.imgur.com/26UNnLS.png)
* R=0, D=2, Value = “555 987 6543”
    * R=0은 root부터 definition level까지의 새로운 record라는 의미
    * D=2(max)는 값이 정의되었다는 의미
* R=1, D=1
    * R=1은 level1의 새로운 entry라는 의미
    * D=1은 contacts는 정의되었지만, phoneNumber는 그렇지 않음을 의미
* R=0, D=0
    * root로부터 새로운 record. contacts가 null

> 각각의 primitive type에 대하여 3개의 sub columns를 만듦으로써 효과적으로 저장
> 하지만, 3 sub columns를 저장할 때 컬럼 기반 저장 방식에 덕분에 오버헤드가 낮다.
> 왜냐하면, level이 스키마의 depth에 한정되기 때문
    > 1bit은 1까지, 2bit은 3까지, 3bit은 7level까지 저장 가능
    > level은 0부터 column의 depth까지 가능
    > repeated field가 아니면 repetition level 불필요, required field는 definition level이 필요 없기 때문에 level 상한선이 더 낮아질 여지가 있다.
> 특별히, flat schema w/ all fields required 인 경우, 2 level은 완전히 무시 가능.

## Parquet 파일 구조
![](https://i.imgur.com/L85naxB.png)
![](https://i.imgur.com/LgqWgGZ.png)

* Parquet 파일은 헤더, 하나 이상의 블록, footer로 구성
    * 헤더 : Parquet 포맷 파일임을 알려주는 4byte magic number : PAR1
    * 블록 : row group. 행에 대한 column 데이터를 포함한 column chunk. 각 column chunk는 페이지에 기록
    * footer : 포맷 버전, 스키마, 추가 키-값 쌍, 파일의 모든 블록에 대한 메타 데이터
* 페이지는 데이터/접근의 최소 단위
    * 동일 컬럼의 데이터만 존재
    * 인코딩/압축 시 페이지 단위로
    * 효율적인 압축을 위해 충분히 커야 함 (but, < 1MB)
* Row group
    * max size buffered in memory while reading
    * One (or more) per split while reading(?)
    * roughly 50MB < row group < 1GB
* Column chunk
    * 한 row group 내 한 컬럼의 데이터
    * 효율적인 scan을 위해 독립적으로 읽힐 수 있음

## Parquet 설정
속성|타입|기본값|설명
---|---|---|---
parquet.block.size|int|128MB|블록 크기 (row group)
parquet.page.size|int|1MB|페이지 크기
parquet.dictionary.page.size|int|1MB|일반 인코딩으로 돌아가기 전 사전의 최대 허용 크기
parquet.enable.dictionary|boolean|true
parquet.compression|String|UNCOMPRESSED|압축 종류<br> - SNAPPY, GZIP, LZO

* 블록의 크기를 늘리면 더 많은 행을 가지므로 순차 I/O 성능을 높일 수 있어 효율적으로 스캔
* 하지만, 개별 블록을 읽고 쓸 때 모든 데이터가 메모리에 저장되어야 하기 때문에 너무 큰 블록을 쓰는 것은 한계가 있음
* Parquet 블록과 HDFS 블록의 기본값은 128MB
* 페이지는 최소 저장 단위.
* 행을 읽기 위해서 페이지를 압축 해제하고 디코딩
* 페이지가 작을 수록 효율적이지만(무엇이?), 크기가 작으면 필요한 페이지 수가 늘어남에 따라 메타 데이터가 많아짐. 이에 따라 저장 용량과 처리 시간이 증가

## Parquet, Avro & ORC
포맷||
---|---
Avro|행 기반
ORC|컬럼 기반<br>Hive 내에서 읽고, 쓰고, 데이터를 처리하는 데 최적화<br>ORC file은 stripe로 구성.<br>각 stripe은 index, 행 데이터, footer로 구성(count, max, min, sum과 같은 주요 statistics들이 cach된 곳)
Parquet|컬럼 기반<br>Parquet 파일은 row group, header, footer를 구성<br>각 row group에 해당하는 column 데이터는 같이 저장

## Parquet 파일 잘 써보기
* Parquet 파일 하나는 1개 이상의 Row group이 있고 Row group은 Column 별로 저장되어있다.
* Row group 크기를 크게 하면 Column 데이터가 연속적으로 저장이 되는 부분이 커져 연산 속도나 압축 효율이 좋아진다.

하지만 Disk block 크기까지 고려한다면….

> A: 커다란 Parquet file에 커다란 Row group 인 경우
연산 속도나 압축 효율은 증가하겠지만, 하나의 Parquet file/Row group이 두 개 Disk block에 걸쳐 있을 확률이 높기 때문에 추가적인 Disk I/O가 발생한다.

> B: 작은 Parquet file과 작은 Row group 인 경우
A처럼 두 개의 Disk block에 걸치는 경우는 적겠지만 columnar storage의 장점이 줄어든다.
(row-based 저장하는 것과 비슷해질 수도...)

> C: 1개의 Parquet file에 1개의 Row group가 1개의 disk block에 있으면 가장 좋다. (Ideal!!!!)
> 
[출처] [[Parquet]아파치 파케이란?](https://m.blog.naver.com/PostView.nhn?blogId=kgw1988&logNo=221227551307&proxyReferer=https:%2F%2Fwww.google.com%2F) | Reve

[출처] [Parquet과 Avro](https://getto215.github.io/parquet-avro/) | getto215

[출처] [Demystify Hadoop Data Formats: Avro, ORC, and Parquet](https://towardsdatascience.com/demystify-hadoop-data-formats-avro-orc-and-parquet-e428709cf3bb) | Xinran Waibel

[출처] [Parquet 파일 잘 써보기](https://dol9.tistory.com/266) | 조급하지말고 천천히

[출처] [Parquet (파케이)](https://devidea.tistory.com/92) | devJoshua

[출처] [Apache Spark에서 컬럼 기반 저장 포맷 Parquet(파케이) 제대로 활용하기](http://engineering.vcnc.co.kr/2018/05/parquet-and-spark/) | Sangwoo Kim, MunShik JOUNG

[출처] [Dremel made simple with Parquet](https://blog.twitter.com/engineering/en_us/a/2013/dremel-made-simple-with-parquet.html) | @J_