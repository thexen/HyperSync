# API 

***사용 가능한 모든 API 안내서.***

----

## Syntax

**API 사용 구문을 정의 합니다.**

### Request 
HTTP Request Syntax를 사용하며

```
METHOD URI HTTP/1.1
```

Request의 URI는 `cluster`섹션에 등록한 `named-cluster`가 포함하여야 합니다.

```
METHOD /{named-cluster}/{URI} HTTP/1.1
```

!!! note ""
    GET /replica-name1/1.txt HTTP/1.1

### Authorization
Request Header에는 `Authorization`이 포함되어 있어야 합니다. 
단 `cluster.users`에 `anonymous` 익명 사용자를 허용 할 경우 인증절차 없이 사용 가능합니다.

```
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    GET /replica-name1/1.txt HTTP/1.1    
    Authorization: Basic YWRtaW46YWRtaW4=

[참고 RFC 7235, section 4.2: Authorization](https://tools.ietf.org/html/rfc7235#section-4.2)

----------------------------------------------------------------------------------------------------------------

## Synchronization

**동기화 API를 설명합니다.**

사용자가 접속한 `peer`에서 Synchronization API를 사용 할 경우 `cluster`에 참여한 모든 `peer`들에게 실시간으로 동일한 API가
적용되며 Upload, Copy, Sync를 호출 하였다면 Content 수정 시간도 함께 동기화가 됩니다.

API결과는 JSON으로 응답을 받게 되며 `uri query`에 `pretty`를 추가하여 요청 하면 가독성이 좋은 JSON으로 응답을 받을 수 있습니다.


### Upload

Content를 업로드 합니다.
Header에는 `Content-Length` 또는 `Transfer-Encoding: chunked`를 포함 하여야 합니다.

Content를 업로드 하면 temporary 경로에 저장하며 업로드가 완료 되었을 경우 service 경로로 이동됩니다.
이때 service 경로에 업로드 Content와 동일한 이름의 Content가 존재 한다면 Content 수정 시간을
기준으로 업로드한 Content로 변경할지 유지할지 결정하게 됩니다.

service 경로에 있는 Content가 업로드 한 Content의 수정 시간보다 최신이면 업로드 한 Content로 변경되지 않고
유지 됩니다.
이 기능을 무시 하시려면 `uri query`에 `purge`를 추가 하여 요청 하여야 합니다.

!!! tip "purge 사용 방법"
    PUT /{named-cluster}/{URI}?purge HTTP/1.1

```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
Content-Length: {`size`}
```
```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
Transfer-Encoding: chunked
```

!!! note ""
    PUT /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    Content-Length: 20    
    
    12345678901234567890    

!!! note ""
    PUT /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    Transfer-Encoding: chunked    
    
    5    
    12345    
    5     
    67890     
    5    
    12345    
    5     
    67890    
    0

[참고 RFC 7230, section 3.3.1: Transfer-Encoding](https://tools.ietf.org/html/rfc7230#section-3.3.1)

부분 업로드 기능을 제공합니다.
업로드 요청시 Header에 `Range`를 추가하여 요청하면 file offset을 변경하여 저장하게 됩니다.

```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
Content-Length: {size}
Range: bytes={start offset- end offset}
```
   
!!! note ""
    PUT /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    Range: bytes=10-    
    Content-Length: 20    
    
    12345678901234567890    

[참고 RFC 7233, section 3.1: Range](https://tools.ietf.org/html/rfc7233#section-3.1)

### Create Directory

Directory를 생성합니다.
recursive가 적용되어 Parent Directory도 함께 생성됩니다.

```
PUT /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    PUT /replica-name1/doc/?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    

!!! danger "주의"
    URI는 '/' 으로 끝나야 합니다.

### Copy

Content를 복사합니다.
`X-Hyper-Copy-Source` 에서 `/{named-cluster}/{URI}`로 파일 복사가 진행 됩니다.

```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
X-Hyper-Copy-Source: /{named-cluster}/{URI}
```

!!! note ""
    PUT /replica-name1/copy.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    X-Hyper-Copy-Source: /replica-name1/1.txt    

Directory 복사 기능을 제공합니다.
recursive가 적용되어 Dest Directory는 Parent Directory를 포함하여 생성 됩니다.

```
PUT /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
X-Hyper-Copy-Source: /{named-cluster}/{URI}/
```

!!! note ""
    PUT /replica-name1/dest/?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    X-Hyper-Copy-Source: /replica-name1/src/   

!!! danger "주의"
    URI는 '/' 으로 끝나야 합니다.    

### Rename

Content를 이동하거나 이름 바꾸기를 합니다.
`X-Hyper-Rename-Source` 에서 `/{named-cluster}/{URI}`로 이동하거나 이름을 변경합니다.

```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
X-Hyper-Rename-Source: /{named-cluster}/{URI}
```

!!! note ""
    PUT /replica-name1/rename.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    X-Hyper-Rename-Source: /replica-name1/1.txt     

Directory 이동과 이름 바꾸기 기능을 제공합니다.

```
PUT /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
X-Hyper-Rename-Source: /{named-cluster}/{URI}/
```

!!! note ""
    PUT /replica-name1/dest/?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    X-Hyper-Rename-Source: /replica-name1/src/    

!!! danger "주의"
    Directory의 URI는 '/' 으로 끝나야 합니다.  


### Change Modified Time

Content의 수정 시간을 변경합니다.

```
PUT /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
X-Hyper-Set-Filetime: {new time}
```

!!! note ""
    PUT /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    
    X-Hyper-Set-Filetime: Sun, 02 Aug 2020 07:28:00 GMT      

### Delete

Content를 삭제합니다.

```
DELETE /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    DELETE /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    

Directory 삭제 기능을 제공합니다.

```
DELETE /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    DELETE /replica-name1/dest/?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    

!!! danger "주의"
    Directory의 URI는 '/' 으로 끝나야 합니다.  

----------------------------------------------------------------------------------------------------------------

### Find

!!! tip ""
    cluster에 참여한 peer들의 동기화 상태를 확일 할 수 있습니다.

`cluster`에 참여한 모든 `peer`에서 Content의 정보를 구합니다.

```
FIND /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    FIND /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=    

`cluster`에 참여한 모든 `peer`에서 Directory의 정보를 구합니다.

```
FIND /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    FIND /replica-name1/dest/?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4= 

!!! danger "주의"
    Directory의 URI는 '/' 으로 끝나야 합니다.  

----------------------------------------------------------------------------------------------------------------

### Sync

사용자가 접속한 `peer`의 Content를 `cluster`에 참여한 모든 `peer`로 push 합니다.

```
SYNC /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    SYNC /replica-name1/1.txt?pretty HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=  

----------------------------------------------------------------------------------------------------------------

### Download

Content를 다운로드 합니다.

```
GET /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    GET /replica-name1/1.txt HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=  

### Get Directory

Directory의 Content 목록을 가져옵니다.
만약 Directory가 아닌 File 인경우 Content Download로 전환 됩니다.

```
GET /{named-cluster}/{URI}/ HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    GET /replica-name1/dest/ HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=  


### Get Attribute

Content의 정보를 구합니다.

```
HEAD /{named-cluster}/{URI} HTTP/1.1
Authorization: Basic  {base64(user/password)}
```

!!! note ""
    HEAD /replica-name1/1.txt HTTP/1.1    
    Host: 127.0.0.1:8080    
    Connection: Close   
    Authorization: Basic YWRtaW46YWRtaW4=  
