# Logging Configurations 

***로깅 환경 설정 안내서***

----

## 로그 설정

두 종류의 로그를 저장 합니다.

* trace
* access

`trace`로그는 **painc**, **error** 등 **debug** 정보나 **issue** 처리를 위한 로그 입니다.

`access`로그는 `clustering` 작업 처리 결과를 저장하는 로그이며 **ElasticSearch**와 직접 연계가 가능하도록 **JSON** 형태로 저장됩니다.


    logs:
      trace:
        path:
        debug: true
        level: 99
        rotacnt: 30
        rotafreq: 86400
      access:
        path:
        debug: false
        level: 0
        rotacnt: 30
        rotafreq: 86400


* `path` - 로그가 저장될 경로
* `debug` - 소스 위치 출력
* `level` - 저장할 로그의 범위 정의
* `rotacnt` - 로그 로테이션 갯수
* `rotafreq` - 로그 로테이션 주기(sec)

!!! tip ""
    HyperCluster의 로그는 rotation 기능이 있습니다.

