Parquet
===

## Table of Contents

[TOC]

## Parguet(파케이)란?

* 데이터를 저장하는 방식(파일 포맷) 중 하나

하둡에 데이터를 저장한다는 것은 일반적으로 빅데이터를 저장한다는 이야기. 따라서 데이터를 효율적으로 저장하기 위해 다음 3가지 사항 필요

1. 빠른 읽기
2. 좋은 압축률
3. 특정 언어에 종속되지 않음

>  Hadoop 에코시스템 내 어떤 프로젝트에서도 이용가능한 **컬럼 기반의 저장 포맷**.
>  특정 데이터처리 프레임워크, 데이터 모델, 프로그래밍 언어에 구애받지 않음

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
각 컬럼의 데이터들은 같은 타입이기 때문에

## Parquet 파일 구조
![](https://i.imgur.com/L85naxB.png)
![](https://i.imgur.com/LgqWgGZ.png)

* Parquet 파일은 헤더, 하나 이상의 블록, footer로 구성
    * 헤더 : Parquet 포맷 파일임을 알려주는 4byte magic number : PAR1
    * 블록 : row group. 행에 대한 column 데이터를 포함한 column chunk. 각 column chunk는 페이지에 기록
    * footer : 포맷 버전, 스키마, 추가 키-값 쌍, 파일의 모든 블록에 대한 메타 데이터
* 페이지는 데이터의 최소 단위
    * 동일 컬럼의 데이터만 존재
    * 인코딩/압축 시 페이지 단위로

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

