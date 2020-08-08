# 용어 사전

----

## Lead Peer
사용자가 `Cluster`를 사용하기 위해 연결한 `Peer`.

사용자가 Clustering 명령 ***Lead Peer***에게 요청하면 사용자가 요청한 `cluster`에 참여한 `peer`에게
***Lead Peer***가  수행 명령을 전달 하고 수집하여 사용자에게 응답합니다.

----

## Base Peer
사용자가 `SYNC` 또는 `PUSH` 명령을 전달하고 결과를 수집 집계하기 위해 연결한 `peer`. 

사용자가 `SYNC` 명령을 ***Lead Peer***에게 요청하면 ***Lead Peer***는 ***Base Peer***로 임시 전환 되며 사용자가 요청한 `cluster`에 첨여 한 `Peer`중에서 임시 ***Lead Peer***를 선출 하여 ***Base Peer***와 통신을 하게 됩니다.
***Base Peer***에 있는 Content를 임시 ***Lead Peer***에게 전송하여 `cluster`에 참여한 다른 모든 `peer`에게도 전달이 가능하도록 합니다.

----

## SYNC vs PUSH

|내용| SYNC | PUSH |
|:---:|:---:|:---:|
|대상|***cluster에 참여한 모든 peer***|***cluster에 참여한 단일 peer***|
|사용 목적|***전체 동기화***|***부분 동기화***|
| Header 추가 |없음|***X-Hyper-Push-Destination***|
 
* `FIND?recursive` 명령으로 동기화 상태를 분석 후 특정 peer에 파일이 누락 되었거나, 동기화가 되어 있지 않은 경우
***PUSH***를 사용합니다.

----