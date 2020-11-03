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


##### [출처] [[Parquet]아파치 파케이란?](https://m.blog.naver.com/PostView.nhn?blogId=kgw1988&logNo=221227551307&proxyReferer=https:%2F%2Fwww.google.com%2F) | Reve
##### [출처] [Parquet과 Avro](https://getto215.github.io/parquet-avro/) | getto215
##### [출처] [Demystify Hadoop Data Formats: Avro, ORC, and Parquet](https://towardsdatascience.com/demystify-hadoop-data-formats-avro-orc-and-parquet-e428709cf3bb) | Xinran Waibel

