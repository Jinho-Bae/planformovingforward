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
![](https://i.imgur.com/AoNnhcg.png "example with three 3 columns")

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

## Parguet, Avro & ORC
포맷||
---|---
Avro|행 기반
ORC|컬럼 기반<br>Hive 내에서 읽고, 쓰고, 데이터를 처리하는 데 최적화<br>ORC file은 stripe로 구성.<br>각 stripe은 index, 행 데이터, footer(count, max, min, sum과 같은 주요 statistics들이 cach된 곳)
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

출처: https://dol9.tistory.com/266 [조급하지말고 천천히]


##### [출처] [[Parquet]아파치 파케이란?](https://m.blog.naver.com/PostView.nhn?blogId=kgw1988&logNo=221227551307&proxyReferer=https:%2F%2Fwww.google.com%2F) | Reve
##### [출처] [Parquet과 Avro](https://getto215.github.io/parquet-avro/) | getto215
##### [출처] [Demystify Hadoop Data Formats: Avro, ORC, and Parquet](https://towardsdatascience.com/demystify-hadoop-data-formats-avro-orc-and-parquet-e428709cf3bb) | Xinran Waibel##### 
[출처] [Parquet 파일 잘 써보기](https://dol9.tistory.com/266) | 조급하지말고 천천히