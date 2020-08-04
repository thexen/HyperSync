
# 준비중...

   ***본 테스트는 API 사용 방법을 포함합니다.***

   본 테스트는 Tutorials의 [Setting up an Replica On Single-Host](replica.md)의 구성을 사용합니다.
![대체 텍스트](../images/github.png)

----

## 테스트 방법

!!! tip ""
    우리는 테스트를 위해 ***Chrome Talend API Test Tool***을 사용 할 것이며    
    동기화 요청에 사용한 HTTP Request와 동기화 요청 결과의 JSON만을 표시합니다.    
    단 업로드 테스트만 테스트 방법을 설명드리고자 전체 화면 캡쳐로 진행합니다.

----

## 업로드 

###### 업로드 전 peer volume 내용 확인

그림을 보시면 모든 `peer`의 `volume`은 파일이 없는 상태입니다.
테스트 진행 전  `peer`의 `volume`을 확인하면서 진행 하시기 바랍니다.

![업로드전](../images/upload-001.png)
 

###### 업로드 요청하기
20bytes 크기의 1.txt 파일을 업로드 하겠습니다.

!!! tip ""
    upload api

```
PUT /replica-name1/11.txt?pretty HTTP/1.1
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


###### 업로드 결과

`peer`의 `volume`을 보시면 20bytes의 1.txt가 저장 되었습니다.

![업로드 결과 ](../images/upload-002.png)


업로드 요청 결과는 아래 JSON과 같이 ***Chrome Talend API Test Tool***에서 확인이 가능합니다.
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

----

