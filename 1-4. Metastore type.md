# Metastore type
***
메타스토어는 실행 유형에 따라 3가지 타입이 있습니다. 각 타입은 다음과 같습니다. 메타스토어를 실행하면 하이브에서 필요한 기본 데이터베이스, 테이블을 확인하고 자동으로 생성합니다.

![MT](https://i.stack.imgur.com/vZjQY.png)

# Embedded metastore
***
기본설정의 하이브는 더비 DB를 사용하게 됩니다. 임베이디드 메타스토어는 한번에 한명의 유저만 접근할 수 있습니다. 따라서 주로 테스트 목적으로만 이용하는 것을 추천합니다.

## hive-site.xml 설정
```
<property>
   <name>javax.jdo.option.ConnectionURL</name>
   <value>jdbc:derby:metastore_db;create=true </value>
   <description>JDBC connect string for a JDBC metastore </description>
</property>
```
# Local metastore
***
로컬 메타스토어는 하이브와 같은 JVM에서 동작합니다. 메타 데이터는 외부의 RDBMS에 저장합니다. 따라서 여러 사용자가 동시에 이용할 수 있습니다.

## hive-site.xml 설정
```
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://[IP]:[port]/[데이터베이스 이름]</value>
    <description>username to use against metastore database</description>
</property>

<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>org.mariadb.jdbc.Driver</value>
    <description>username to use against metastore database</description>
</property>

<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>[사용자명]</value>
    <description>username to use against metastore database</description>
</property>

<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>[암호]</value>
    <description>password to use against metastore database</description>
</property>
```

# Remote metastore
***
원격 메타스토어는 메타스토어가 별도의 JVM에서 동작합니다. 쓰리프트 프로토콜을 이용하여 접속합니다.

## hive-site.xml 설정
```
<property>
    <name>hive.metastore.uris</name>
    <value>thrift://[메타스토어 IP]:[메타스토어 Port]</value>
    <description>JDBC connect string for a JDBC metastore</description>
</property>
```

# Meta Database
***
하이브의 테이블들에 대한 논리적인 정보는 데이터베이스에 저장됩니다. 메타스토어 서비스는 이 데이터베이스 정보를 여러 클라이언트에게 제공합니다.

데이터베이스는 더비, MSSQL, MySQL, 오라클 등을 지원합니다. 이 데이터베이스에 하이브 서비스에 필요한 스키마를 미리 생성하고 서비스합니다. 하이브 클라이언트를 이용해서 확인하는 정보는 이 데이터베이스의 정보에 기반하고 있습니다. 따라서 하이브 테이블 관련하여 필요한 정보를 확인하고 싶을 때는 데이터베이스에 직접 접속하여 확인 해도 됩니다.

# 주요테이블
***
하이브 데이터베이스의 주요 테이블은 다음과 같습니다.

테이블명|	설명
--|--
DBS	|데이터베이스 정보
TBLS	|테이블 정보
PARTITIONS|	파티션 정보

# 메타 데이터 확인
***
SQL 쿼리를 이용하여 메타 데이터를 확인하는 방법은 다음과 같습니다.

## 메타 데이터 확인
다음과 같이 메타 데이터를 확인할 수 있습니다.

```
-- DB 정보
SELECT *
  FROM DBS;


-- 테이블 정보
SELECT *
  FROM TBLS;


-- DB, 테이블 조인
SELECT *
  FROM TBLS t, DBS d
 WHERE t.DB_ID = d.DB_ID
 ORDER BY d.NAME;

-- 테이블 이름에 sample 이 들어가는 테이블을 찾아서, 디비명, 테이블명, 파티션명을 출력
SELECT d.NAME, t.TBL_NAME, p.PART_NAME
  FROM DBS d, TBLS t, PARTITIONS p
 WHERE d.DB_ID = t.DB_ID
   AND t.TBL_ID = p.TBL_ID
   AND t.TBL_NAME like '%sample%';
   

```

# 전체 테이블
***
하이브 메타스토어의 전체 테이블은 다음과 같습니다.
```
+---------------------------+
| Tables_in_hive            |
+---------------------------+
| AUX_TABLE                 |
| BUCKETING_COLS            |
| CDS                       |
| COLUMNS_V2                |
| COMPACTION_QUEUE          |
| COMPLETED_COMPACTIONS     |
| COMPLETED_TXN_COMPONENTS  |
| DATABASE_PARAMS           |
| DBS                       |
| DB_PRIVS                  |
| DELEGATION_TOKENS         |
| FUNCS                     |
| FUNC_RU                   |
| GLOBAL_PRIVS              |
| HIVE_LOCKS                |
| IDXS                      |
| INDEX_PARAMS              |
| KEY_CONSTRAINTS           |
| MASTER_KEYS               |
| NEXT_COMPACTION_QUEUE_ID  |
| NEXT_LOCK_ID              |
| NEXT_TXN_ID               |
| NOTIFICATION_LOG          |
| NOTIFICATION_SEQUENCE     |
| NUCLEUS_TABLES            |
| PARTITIONS                |
| PARTITION_EVENTS          |
| PARTITION_KEYS            |
| PARTITION_KEY_VALS        |
| PARTITION_PARAMS          |
| PART_COL_PRIVS            |
| PART_COL_STATS            |
| PART_PRIVS                |
| ROLES                     |
| ROLE_MAP                  |
| SDS                       |
| SD_PARAMS                 |
| SEQUENCE_TABLE            |
| SERDES                    |
| SERDE_PARAMS              |
| SKEWED_COL_NAMES          |
| SKEWED_COL_VALUE_LOC_MAP  |
| SKEWED_STRING_LIST        |
| SKEWED_STRING_LIST_VALUES |
| SKEWED_VALUES             |
| SORT_COLS                 |
| TABLE_PARAMS              |
| TAB_COL_STATS             |
| TBLS                      |
| TBL_COL_PRIVS             |
| TBL_PRIVS                 |
| TXNS                      |
| TXN_COMPONENTS            |
| TYPES                     |
| TYPE_FIELDS               |
| VERSION                   |
| WRITE_SET                 |
+---------------------------+
```

# Metastore database 설정
***
원격 하이브 메타스토어를 이용하기 위해서는 MySQL, 오라클 같은 RDB에 하이브 메타스토어 스키마를 설정해야 합니다. `hive-site.xml`에 RDB에 접속을 위한 정보를 입력하고, 커맨드를 입력하여 스키마를 생성할 수 있습니다.

## hive-site.xml 설정
하이브의 conf아래 hive-site.xml에 다음과 같이 데이터 베이스 접속을 위한 정보를 입력합니다.

```
<?xml version="1.0"?>
<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://database_ip:database_port/database_name</value>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>org.mariadb.jdbc.Driver</value>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>user_name</value>
  </property>

  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>password</value>
  </property>
</configuration>
```

# 스키마 생성
***
스키마 생성 방법은 다음과 같습니다. dbType에 사용하는 데이터베이스를 입력하면 됩니다. mssql, mysql, oracle, postgres를 지원합니다.

```
$ hive --service schemaTool -dbType mysql -initSchema
```

# 스키마 업그레이드
***
하이브 버전을 바꾸게 되면 하위버전의 스키마를 상위버전으로 업그레이드 해야 하는 경우가 생깁니다. 이럴때는 다음과 같이 사용합니다.

```
$ hive --service schemaTool -dbType mysql -upgradeSchema
```

# 스키마 정보 확인
***
하이브 스키마의 정보를 확인하는 방법은 다음과 같습니다.

```
$ hive --service schemaTool -dbType mysql -info
Metastore connection URL:      jdbc:mysql://database_ip:database_port/database_name
Metastore Connection Driver :  org.mariadb.jdbc.Driver
Metastore connection User:     user_name
Hive distribution version:     2.3.0
Metastore schema version:      2.3.0
schemaTool completed
```

