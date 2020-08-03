# Configurations 

***클러스터링 환경 설정 안내서***

----

## 환경 변수

* `HYPERCLUSTER_PEER_HOSTNAME` - hostname 설정.(default: Windows의 경우 컴퓨터 이름, Linux의 경우 hostname으로 자동 설정됩니다.)
* `HYPERCLUSTER_PEER_PREFERENCE_FILE` - 클러스터 설정 파일.(default: `./config/preference.yaml`)
* `HYPERCLUSTER_PEER_LOG_PREFERENCE_FILE` - 로그 설정 파일.(default: `./config/log.yaml`)
* `HYPERCLUSTER_PEER_TLS_ENABLE` - TLS 사용 여부.(default: `DISABLE`)
* `HYPERCLUSTER_PEER_TLS_CERT` - `crt` 파일.(default: `./cert/public.crt`)
* `HYPERCLUSTER_PEER_TLS_KEY` - `key` 파일.(default: `./cert/private.key`)
* `HYPERCLUSTER_PEER_ADVERTISE_URL` - cluster peer 관리 서버 URL.( `미지원상태`)

----

## 클러스터 설정

`preference.yaml`파일에는 4개의 섹션이 있습니다.

* volumes
* peers
* users
* cluster

`volumes`섹션은 `peer`가 사용 할 `volume`을 준비 할 수 있습니다.
**service** 경로와 **temporary**경로를 설정하여야 하며 `peer`는 **upload**와**sync** 명령을 요청 받으면  Content를 저장하게 되는데 **service** 중인 파일이 오염되지 않도록 **temporary** 경로로 저장한 후 전송이 완료 되면 **service**중인 Content 와 Swap을 하여 **service**중인 파일의 무결성을 보장 합니다.

`peers`섹션은 **hostname**, **address:port**, **volumes**를 설정하여 `cluster`에 참여 할 수 있도록 준비 합니다.

`users`섹션은 `cluster`를 사용 할 **user**를 정의 하며 비밀번호와 허용IP를 등록합니다.
허용IP는 `users.{user}.net`에 `CIDR`표기 방식으로 입력하면 됩니다.

`cluster`섹션은 **named-cluster**로 여러개의 **group**으로 구성 할 수 있고 **named-cluster** 마다 **role**을 정의하여 다른 역할을 가진 `cluster`로 구성을 할 수 있습니다. **named-cluster**에 참여할 `peer`와 `user` 그리고 `access log`를 저장할 **ElasticSearch URL** 정보를 등록 하면 설정이 마무리 됩니다.
`cluster`의 **role**은 **restapi**, **replica**, **advertisement** 가 있습니다.

`cluster` 설정 파일인 `preference.yaml`의 기본 구조를 설명 하겠습니다.

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
   
    #Section: users
    users:
      admin:
        pwd: admin
        net: 
          - 127.0.0.1/24
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


`volumes`섹션에서 named-volume 인 volume-name1를 정의 하였습니다.
`peers`섹션의 hostname1.volumes 에서 정의한 `named-volume` 인 **repo-name1**는 `volumes`에서 정의한 volume-name1을 `mount` 하였습니다.

`peer`의 `volumes`는 `volumes`섹션에서 정의 하지 않아도 `named-volume`을 `mount` 할 수 있습니다.
 `peers`섹션의 **hostname1**는 Winodws에서는 컴퓨터 이름, Linux에서는 Host Name과 동일 하여야 합니다.

!!! warning ""
    `peers`섹션의
    **hostname1**는 Winodws에서는 컴퓨터 이름, Linux에서는 Host Name과 동일 하여야 하며 **addr**는 **peer** 간 통신이 가능한 **ip:port**로 설정 되어야 합니다.
   
`cluster`섹션에서 정의한 **named-cluster** 인 **replica-name1**의 설정을 보면  ***실시간 동기화*** 기능을 사용하기 위해 **role**을 **replica**로 설정 하였습니다.

**replica-name1**에 참여한 `peer`는 **hostname1**이며 
**hostname1**이 사용 할 `volume`은 `peers`센셕에서 정의한 **hostname1**의 **named-volume** **repo-name1** 사용하도록 설정 하였습니다.

**replica-name1**에 접근 가능한 사용자는 `users`센션에서 정의된 **admin**와 **user1** 이고 익명 사용자인 `anonymous`도 접근이 가능하게 설정 하였습니다.
**admin**의 `grant`를 **rwds** 설정하여 모든 권한을 부여하였고 **user1**과 `anonymous`는  가 `r`인 읽기 권한만 부여하였습니다.

!!! note ""
    `grant`는 `r`: 읽기 권한, `w`: 쓰기 권한, `d`: 삭제 권한, `s`: 수동 동기 권한 이 있습니다.

!!! danger "주의"
    `users`에 `anonymous`을 추가 하면 익명사용자 이므로 인증을 하지 않으니 주의 하여야 합니다.

