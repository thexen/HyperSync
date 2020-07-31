# Configurations 

## 환경 변수

* `HYPERCLUSTER_PEER_PREFERENCE_FILE` - 클러스터 설정 파일.(default: `./config/preference.yaml`)
* `HYPERCLUSTER_PEER_LOG_PREFERENCE_FILE` - 로그 설정 파일.(default: `./config/log.yaml`)
* `HYPERCLUSTER_PEER_TLS_ENABLE` - TLS 사용 여부.(default: `DISABLE`)
* `HYPERCLUSTER_PEER_TLS_CERT` - `crt` 파일.(default: `./cert/public.crt`)
* `HYPERCLUSTER_PEER_TLS_KEY` - `key` 파일.(default: `./cert/private.key`)
* `HYPERCLUSTER_PEER_ADVERTISE_URL` - cluster peer 관리 서버 URL.( `미지원상태`)

## 클러스터 설정

`preference.yaml`파일에는 4개의 섹션이 있습니다.

* volumes
* peers
* users
* cluster

`volumes`섹션은 peer가 사용 할 volume을 준비 할 수 있습니다.
`service`경로와 `temporary`경로를 설정하여야 하며 peer는 `upload`와`sync` 명령을 수신하면 파일을 저장하는데 `service`중인 파일이 오염되지 않도록 `temporary`경로를 이용하여 저장한 후 `service`경로로 파일을 이동 시켜 `service`중인 파일의 무결성을 보장 합니다.

`peers`는 `hostname`, `peer address:port`, `volumes`를 설정하여 `cluster`에 참여 가능하도록 합니다.

`user` 섹션은 cluster를 사용 할 `user`를 정의 하며
`cluster` 세션은 `cluster name`으로 `cluster`를 여러 `group`으로 구성 할 수 있고 `group`마다 `role`을 정의하여 각기 다른 역할을 가진 `cluster` 구성이 가능합니다.
`cluster name`에 참여할 `peer`와 `user` 그리고 `access log`를 저장할 `ElasticSearch URL` 정보를 등록 하면 설정이 마무리 됩니다.
`cluster`의 `role`은 `restapi`, `replica`, `advertisement` 가 있으며 현재 `replica` 만 지원 합니다.

`preference.yaml` 파일의 구조를 보시겠습니다.

    #Section: volumes
    volumes:
      volume-name1: 
        temporary: 
        service: 
    
    #Section: peers    
    peers:
      hostname1:
        addr: 
        volumes: 
          repo-name1: volume-name1
          repo-name2:
            temporary: 
            service: 
   
    #Section: user
    users:
      admin:
        pwd: admin
      user1:
        pwd: user1						

    #Section: cluster          
    cluster: 
      restapi:
        role: restapi
        elasticsearch: 
        api:
          - status
        peers:
          - hostname1
        users:
          - admin
      replica-name1:
        role: replica
        elasticsearch-url: 
        peers:
          hostname1: 
            volume: repo-name1
        users:
          admin:
            grant: rwds
          user1:
            grant: r
          anonymous:
            grant: r
       replica-name2:
         role: advertisement
         elasticsearch:
         peers:
         users:


`volumes`섹션에서 `volume-name1`을 정의 하였고 `peers`섹션의 `hostname1.volumes`에서 정의한 `repo-name1`에서 사용합니다.
`peer`의 `volumes`는 `volumes`섹션에서 정의 하지 않아도 자체 정의하여 사용이 가능합니다.
 `peers`섹션의 `hostname1`는 Winodws에서는 컴퓨터 이름, Linux에서는 Host Name과 동일 하여야 합니다.

!!! warning
    `peers`섹션의
    `hostname1`는 Winodws에서는 컴퓨터 이름, Linux에서는 Host Name과 동일 하여야 하며 `addr`은 `peer`들 간 통신이 가능한 `ip:port`로 설정 되어야 합니다.
   
`cluster`섹션에서 정의한 `replica-name1`의 설정을 보면
`replica`역할을 하는 `cluster`로써 ***실시간 동기화***를 가능하게 합니다.

`replica-name1`에 `join`한 `peer`는 `hostname1`이며
`hostname1`이 사용할 `volume`은 `hostname1`에서 정의한 `volume`들 중 `repo-name1`입니다

`replica-name1`에 접근 가능한 사용자는 `users`에 정의된 admin와 user1 이고 익명 사용자인 `anonymous`도 접근이 가능하게 설정 하였습니다.
admin의 권한인 `grant`가 `rwds` 이므로 모든 권한을 가지고 있고 나머지 user1과 `anonymous`는  `grant`가 `r`인 읽기 권한만 가집니다

!!! note "참고"
    `grant`는 `r`: 읽기 권한, `w`: 쓰기 권한, `d`: 삭제 권한, `s`: 수동 동기 권한 이 있습니다.

!!! danger
    `users`에 `anonymous`을 추가 하면 익명사용자 이므로 인증을 하지 않으니 주의 하여야 합니다.

## 로그 설정

두종류의 로그를 저장 합니다.

* trace
* access

`trace`로그는 painc, error 등 debug 정보나 issue 처리를 위한 로그 입니다.

`access`로그는 `clustering` 작업 처리 결과를 저장하는 로그이며 `ElasticSearch`와 직접 연계가 가능하도록 json 형태로 저장됩니다.


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

!!! tip
    HyperCluster의 로그는 자체 rotation 기능을 제공합니다.

