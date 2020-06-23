# 비라인(beeline)
***
비라인은 SQLLine 기반의 하이브서버2(hiveserver2)에 접속하여 쿼리를 실행하기 위한 도구 입니다. JDBC 를 사용하여 하이브서버2에 접속합니다.

# 비라인 접속
***
비라인에서 하이브서버2에 접속하는 방법은 비라인 CLI를 실행 후에 입력하는 방법과 비라인 CLI를 실행하면서 옵션으로 입력하는 방법이 있습니다. 하이브서버2에 접속하면 프롬프트에 접속한 하이브서버2의 접속 정보가 표현됩니다.

하이브서버2의 접근 포트는 `hive.server2.thrift.port`에 설정된 정보를 이용하면 됩니다.

```
# connect 명령을 이용한 접속 방법 
$ beeline
beeline> !connect jdbc:hive2://localhost:10000 scott tiger
0: jdbc:hive2://localhost:10000>

# 커맨드라인 옵션을 이용한 접속 방법 
$ beeline -u jdbc:hive2://localhost:10000 -n scott -p tiger
0: jdbc:hive2://localhost:10000>
```

# 비라인 SQLLine 커맨드
***
비라인에서는 느낌표를 이용하여 SQLLine 커맨드를 입력 할 수 있습니다. SQLLine 커맨드의 상세한 내용은 매뉴얼을 참고하시면 됩니다.

```
0: jdbc:hive2://localhost:10000> !columns tbl;
0: jdbc:hive2://localhost:10000> !quit
0: jdbc:hive2://localhost:10000> !columns tbl;
```

# 비라인 SQLLine 커맨드
***
비라인에서 사용할 수 있는 주요 하이브 커맨드는 다음과 같습니다. CLI와 동일한 커맨드를 이용할 수 있습니다.

커맨드|	설명
--|--
reset	|설정값 초기화
set <key>=<value>|	설정값 입력
set|	하이브의 설정값 출력
set -v|	하둡, 하이브의 설정값 출력
add file <>	|파일 추가
add files <>	|여러개의 파일 추가, 공백으로 경로 구분
add jar <>|	jar 파일 추가
add jars <>	|여러개의 jar 파일 추가, 공백으로 경로 구분
dfs <dfs command>|	하둡 dfs 커맨드 실행

```
0: jdbc:hive2://localhost:10000> set mapred.reduce.tasks=32;
No rows affected (0.044 seconds)

0: jdbc:hive2://localhost:10000> reset;
No rows affected (0.134 seconds)

0: jdbc:hive2://localhost:10000> dfs -ls /user/;
+------------------------------------------------------------------------------+--+
|                                  DFS Output                                  |
+------------------------------------------------------------------------------+--+
| drwxrwxrwx   - hadoop hadoop          0 2018-12-04 07:14 /user/hadoop        |
+------------------------------------------------------------------------------+--+
```

# 출력 포맷
***
비라인은 출력포맷을 설정할 수 있습니다. XML, CSV, TSV 형태로 설정할 수 있기 때문에 처리 결과를 이용하여 다른 작업을 해야 하는 경우에 편리하게 이용할 수 있습니다.
```
# --outputformat=[table/vertical/csv/tsv/dsv/csv2/tsv2]
$ beeline --outputformat=csv -f sample.hql
```


