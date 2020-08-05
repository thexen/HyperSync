
# Synchronization API

   ***본 Tutorial은 API 사용 방법을 포함합니다.***

  본 Tutorial의 **Cluster**구성은 [Setting up an Replica On Single-Host](replica.md)을 참고 바랍니다.
  ![대체 텍스트](../images/github.png)

----

## API 사용방법

!!! tip ""
    우리는 API 호출을 위해 ***Chrome Talend API Test Tool***을 사용 할 것입니다.    
    그리고 HTTP Request와 Request 결과 JSON만을 제공 할 것이고
    업로드 API 사용방법만 화면 캡쳐를 포함하여 제공합니다.

----

## 업로드

##### 업로드 전 peer volume 확인
그림을 보면 모든 `peer`의 `volume`은 파일이 없는 상태입니다.

![업로드전](../images/upload-001.png)
 
##### 업로드 요청하기
20bytes 크기의 1.txt 파일을 업로드 합니다.

!!! tip ""
    UPLOAD API

```
PUT /replica-name1/1.txt?pretty HTTP/1.1
Host: 127.0.0.1:8080
Connection: Close
Authorization: Basic YWRtaW46YWRtaW4=
Content-Length: 20

12345678901234567890
```

!!! danger ""
    Chrome의 Talend API Test 툴을 사용하여 업로드 합니다.    
    uri query에 pretty를 추가 하시면 가독성이 좋은 JSON으로 응답을 받을 수 있습니다.


![업로드 ](../images/upload-000.png)


!!! danger ""
    ***Chrome Talend API Test Tool***를 이용한 파일 업로드 방법은 아래 그림과 같이      
    좌측 영역 HEADERS에서 `Content-Length`를 삭제하고 우측 BODY영역에서 File을 선택하면 파일 업로드가 가능합니다.     
    
![업로드 ](../images/upload-004.png)

##### 업로드 결과
`peer`의 `volume`에서 20bytes의 1.txt를 확인합니다.

![업로드 결과 ](../images/upload-002.png)


업로드 요청 결과는 아래 JSON과 같이 ***Chrome Talend API Test Tool***에서 확인 가능합니다.
```
{
  "tx": "1230c013-6076-4297-bfd0-4af08057d8d3",
  "code": 200,
  "Succeeded": 4,
  "Failed": 0,
  "results": [
    {
      "command": "upload",
      "tx": "1230c013-6076-4297-bfd0-4af08057d8d3",
      "code": 200,
      "host": "hostname2",
      "result": "succeeded",
      "reason": {
        "error": "",
        "where": ""
      },
      "cluster": {
        "cluster_name": "replica-name1",
        "cluster_role": "replica",
        "peers": "hostname0,hostname1,hostname2,hostname3",
        "peers_count": 4,
        "lead_peer": "hostname0",
        "child_peers": ""
      },
      "request": {
        "user": "admin",
        "uri": "/replica-name1/1.txt",
        "query": "pretty",
        "srcuri": "",
        "range": ""
      },
      "stat": {
        "trans_amount": 20,
        "time": "Tue,  4 Aug 2020 12:39:55 GMT",
        "time_taken": "9.9731ms"
      },
      "response": null
    },
    {
      "command": "upload",
      "tx": "1230c013-6076-4297-bfd0-4af08057d8d3",
      "code": 200,
      "host": "hostname3",
      "result": "succeeded",
      "reason": {
        "error": "",
        "where": ""
      },
      "cluster": {
        "cluster_name": "replica-name1",
        "cluster_role": "replica",
        "peers": "hostname0,hostname1,hostname2,hostname3",
        "peers_count": 4,
        "lead_peer": "hostname0",
        "child_peers": ""
      },
      "request": {
        "user": "admin",
        "uri": "/replica-name1/1.txt",
        "query": "pretty",
        "srcuri": "",
        "range": ""
      },
      "stat": {
        "trans_amount": 20,
        "time": "Tue,  4 Aug 2020 12:39:55 GMT",
        "time_taken": "8.9733ms"
      },
      "response": null
    },
    {
      "command": "upload",
      "tx": "1230c013-6076-4297-bfd0-4af08057d8d3",
      "code": 200,
      "host": "hostname1",
      "result": "succeeded",
      "reason": {
        "error": "",
        "where": ""
      },
      "cluster": {
        "cluster_name": "replica-name1",
        "cluster_role": "replica",
        "peers": "hostname0,hostname1,hostname2,hostname3",
        "peers_count": 4,
        "lead_peer": "hostname0",
        "child_peers": "hostname3"
      },
      "request": {
        "user": "admin",
        "uri": "/replica-name1/1.txt",
        "query": "pretty",
        "srcuri": "",
        "range": ""
      },
      "stat": {
        "trans_amount": 20,
        "time": "Tue,  4 Aug 2020 12:39:55 GMT",
        "time_taken": "11.9689ms"
      },
      "response": null
    },
    {
      "command": "upload",
      "tx": "1230c013-6076-4297-bfd0-4af08057d8d3",
      "code": 200,
      "host": "hostname0",
      "result": "succeeded",
      "reason": {
        "error": "",
        "where": ""
      },
      "cluster": {
        "cluster_name": "replica-name1",
        "cluster_role": "replica",
        "peers": "hostname0,hostname1,hostname2,hostname3",
        "peers_count": 4,
        "lead_peer": "hostname0",
        "child_peers": "hostname1,hostname2"
      },
      "request": {
        "user": "admin",
        "uri": "/replica-name1/1.txt",
        "query": "pretty",
        "srcuri": "",
        "range": ""
      },
      "stat": {
        "trans_amount": 20,
        "time": "Tue,  4 Aug 2020 12:39:55 GMT",
        "time_taken": "16.9555ms"
      },
      "response": null
    }
  ]
}
```

!!! tip ""
    ***결과 필드 설명***    

* 요청 처리 결과 집계    
`tx`: transaction id    
`code`: http code    
`Succeeded`: 성공 수   
`Failed`: 실패 수    

* 요청 처리 결과     
`results[].command`: request 명령    
`results[].tx`: transaction id     
`results[].code`: http code    
`results[].host`: peer의 hostname    
`results[].result`: request 명령 처리 결과     
`results[].reason.error`: request 명령 처리 결과 실패 원인    
`results[].reason.where`: request 명령 처리 결과 실패 위치    

* cluster 정보    
`results[].cluster.cluster_name`: named-cluster    
`results[].cluster.cluster_role`: cluster 역할    
`results[].cluster.peers`: cluster에 참여한 peer    
`results[].cluster.peers_count`: cluster에 참여한 peer 수     
`results[].cluster.lead_peer`: lead peer    
`results[].cluster.child_peers`: child peer    

* request 정보    
`results[].request.user`: 사용자    
`results[].request.uri`: uri 정보    
`results[].request.query`: uri query 정보    
`results[].request.srcuri`: 요청한 http header의 source uri 정보    
`results[].request.range`: 요청한 http header의 range 정보    

* 통계    
`results[].stat.trans_amount`: 전송량    
`results[].stat.time`: 요청 처리 시작 시간    
`results[].stat.time_taken`: 요청 처리에 걸린 시간    

* 추가 정보    
`results[].response`: request 처리 결과의 추가 정보     

----

## 디렉토리 만들기

----