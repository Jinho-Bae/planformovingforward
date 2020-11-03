# Apache Sentry & Ranger

## 무엇을 택할까?

어떤 Hadoop distribution tool을 사용하는 지에 따라 Sentry나 Ragner를 선택할 수 있다.
* Apache Sentry
Cloudera 소유. CDH에서 사용. HDFS, Hive, Solr, Impala를 지원
(Ranger는 Impala 미지원)
* Apache Ranger
Hortonworks 소유. HDP에서 사용. 중앙화된 보안 프레임워크 제공. HDFS, Hive, Solr, HBase, Storm, Knox, Kafka, YARN 지원

## 차이점
* Sentry는 **ROLE** 기준
* Ranger는 **USER** 기준

e.g. hive sentry 구성
1. privileges 생성 (data objects + action)

  data objects : tables 

  action : select/update/insert/delete..

2. privileges를 role(신규 또는 기존)에 할당

3. roles를 group(ad생성)에 맵핑

4. group에 user를 맵핑

## Sentry :arrow_right:  Ragner
Cloudera와 Hortonworks는 18년도에 합병. 이 두 회사는 Ranger를 표준화하기로 결정. CDH은 Sentry를 사용하지만, CDH와 HDP를 통합한 CDP에서 Ranger가 이용된다.

Sentry 정책과 Ranger 정책 사이에 1:1 맵핑이 없다.
Sentry 정책은 Ranger에서 어떻게 보여지는가?
* Sentry에서 role에 부여되는 권한(허가)은 Ranger에서 group에 부여
* 부모 객체에게 부여디는 Sentry 권한은 자식 객체 또한 부여
    - e.g. 데이터베이스에 부여되는 권한은 그 데이터베이스에 속한 테이블들에 대해서도 부여
* Sentry OWNER :arrow_right: Ranger ALL
* Sentry OWNER WITH GRANT OPTION :arrow_right: Ranger ALL with Delegated Admin Checked
* Sentry는 table과 view를 구분 짓지 않음
* 

##### [출처] [How to choose between apache ranger and sentry](https://stackoverflow.com/questions/39326456/how-to-choose-between-apache-ranger-and-sentry)
##### [출처] [sentry 보안](https://semode.tistory.com/374) | 세모데
##### [출처] [Sentry to Ranger Permissions](https://docs.cloudera.com/replication-manager/cloud/core-concepts/topics/rm-sentry-ranger-permissions.html) | Cloudera