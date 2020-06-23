# Hive CLI
***
하이브 CLI(Command Line Interface)는 하이브 쿼리를 실행하는 가장 기본적인 도구 입니다. 인터랙티브 쉘을 이용하여 사용자의 명령을 입력할 수 있습니다. 여기서는 쿼리 실행 방법, 유용한 옵션, 내부 명령어를 알아보겠습니다. 상세한 내용은 하이브 매뉴얼1을 참고하면 됩니다.

# CLI 옵션
***
하이브 CLI의 주요 옵션은 다음과 같습니다. hiveconf는 옵션값을 설정할 때 이용합니다. hivevar는 쿼리에 변수를 지정할 때 이용합니다.

```
usage: hive
 -e <quoted-query-string>         커맨드 라인으로 실행할 쿼리 
 -f <filename>                    쿼리가 작성된 파일을 이용하여 실행할 경우 
 --hiveconf <property=value>      하이브 설정값 입력 
                                  예) --hiveconf tez.queue.name=queue
 --hivevar <key=value>            쿼리에서 사용할 변수 입력 
                                  예) --hivevar targetDate=20180101

-- 옵션 지정 방법 
$ hive --hiveconf tez.queue.name=queue --hivevar targetDate=20180101
```

# 쿼리 실행
***
하이브 쿼리 실행은 인터랙티브 쉘에서 입력하는 방법, 커맨드 라인 입력, 파일 입력방법 세가지가 있습니다.

# 인터랙티브 쉘 입력
***
하이브 CLI를 실행하고, 쉘을 이용하여 입력하는 방법입니다.
```
-- hive 실행후 쿼리 입력 
$ hive
hive> select 0;
OK
0
Time taken: 1.367 seconds, Fetched: 1 row(s)

hive> select * from table;
```

# 커맨드 라인 입력
***
커맨드 라인입력은 `-e`옵션을 이용합니다.
```
-- 커맨드 라인에서 바로 입력 
$ hive -e "select * from table"

-- 옵션을 이용하여 쿼리의 실행 엔진과, 변수를 설정 
-- 커맨드 라인입력에서 설정값 전달 
$ hive -e 'SELECT * FROM table WHERE yymmdd=${hivevar:targetDate}' \
   --hiveconf hie.execution.engine=tez \
   --hiveconf tez.queue.name=queue_name \
   --hivevar targetDate=20180101
```

# 파일 입력
***
파일 입력은 쿼리를 파일로 저장해 놓고 해당 파일을 지정하는 방식입니다. `-f` 옵션을 이용합니다.
```
$ cat sample.hql
SELECT * FROM table WHERE yymmdd=${hivevar:targetDate}

-- 파일을 이용하여 쿼리 입력 
$ hive -f sample.hql --hivevar targetDate=20180101
```
## 로깅방법
하이브 CLI는 log4j를 이용하여 로깅합니다. 로깅 방법을 변경하고자 할 때는 log4j 설정 파일을 변경하거나 --hiveconf 옵션을 이용합니다.
```
-- 로깅 레벨, 파일 위치 변경 
hive --hiveconf hive.log.file=hive_debug.log \
  --hiveconf hive.log.dir=./ \
  --hiveconf hive.root.logger=DEBUG,DRFA
```

# CLI 내부 명령어
***
하이브 CLI의 주요 내부 명령어는 다음과 같습니다. 상세한 설명은 하이브 매뉴얼2을 참고하면 됩니다.

커맨드|	설명
--|--
exit|	종료
reset|	설정값 초기화
set <key>=<value>|	설정값 입력
set|	하이브의 설정값 출력
set -v|	하둡, 하이브의 설정값 출력
add file <>	|파일 추가
add files <>|	여러개의 파일 추가, 공백으로 경로 구분
add jar <>|	jar 파일 추가
add jars <>|	여러개의 jar 파일 추가, 공백으로 경로 구분
!<command>|	쉘 커맨드 실행
dfs <dfs command>|	하둡 dfs 커맨드 실행

jar 파일 추가는 사용자가 구현한 UDF, SerDe 등 자바 클래스 사용할 때 jar 파일의 위치를 지정합니다. 파일 추가는 스크립트나 UDF에서 내부적으로 사용하는 메타 파일 등을 추가할 때 사용합니다.
```
-- 옵션 설정 
hive> set mapred.reduce.tasks=32;
-- 모든 옵션 값 확인 
hive> set;
-- CLI 상에서 설정한 옵션 초기화 
hive> reset;
-- 쉘커맨드 실행 
hive> !ls -alh;
-- dfs 커맨드 실행 
hive> dfs -ls /user/;
-- 파일 추가 
hive> add file hdfs:///user/sample.txt;
-- 여러개의 파일 추가. 공백으로 구분 
hive> add files hdfs:///user/sample1.txt hdfs:///user/sample2.txt;
-- jar 파일 추가 
hive> add jar hdfs:///user/sample.jar;
```









