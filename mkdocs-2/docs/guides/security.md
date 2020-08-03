# Security

***보안 설정 안내서***

----

## 사용자 허용 IP 설정

 사용자의 계정별로 접속 허용 IP를 등록 할 수 있습니다.

`users.{user}.net`에 CIDR 표기로 하나 이상 등록이 가능하며,
`users.{user}.net`을 등록하지 않는다면 모든 IP가 허용 됩니다.

```
#Section: users
users:
  admin:
    pwd: admin
    net: #CIDR 표기
      - 127.0.0.1/24       
      - 192.168.243.102/32
  user1:
    pwd: user1				
```

!!! danger "주의"
    `anonymous`는 모든 IP가 허용되며 인증을 하지 않습니다.
 
!!! danger "등록 적용이 되지 않는 경우"
        CIDR 표기가 아닐 경우( 192.168.243 )       
        등록시 `,`로 동시에 여러개 등록 할 경우 ( - 127.0.0.1/24, 192.168.243.102/32 )

----

## Cluster 사용자의 grant 설정

`cluster`사용 허가를 받은 사용자에 한해 `grant` 등록을 할 수 있습니다.
`grant`등록을 하지 않는다면 모든 권한을 부여 받게 됩니다.

!!! note ""
     `r`: 읽기 권한 - GET, HEAD, POST Mehtod    
     `w`: 쓰기 권한 - PUT Method   
     `d`: 삭제 권한 - DELETE Method   
     `s`: 수동 동기 권한 - SYNC Mehod     

!!! note ""
    `grant`등록은 사용자의 계정에서 등록 하는 것이 아닌 `named-cluster`를 사용하는 사용자를 대상으로 등록합니다.    
    `cluster.{named-cluster}.users.{user}.grant` 입니다.

```
    #Section: cluster          
    cluster: 
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
```

----