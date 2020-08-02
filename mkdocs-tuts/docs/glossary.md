# 용어 사전

## Lead Peer
사용자가 `Cluster`를 사용하기 위해 연결한 `Peer`.

사용자가 Clustering 명령 ***Lead Peer***에게 요청하면 사용자가 요청한 `cluster`에 참여한 `peer`에게
***Lead Peer***가  수행 명령을 전달 하고 수집하여 사용자에게 응답합니다.

## Base Peer
사용자가 `SYNC`명령을 전달하고 결과를 응답 받기 위해 연결한 `peer`.
임시로 `SYNC` 명령을 처리를 위해 사용자를 대신하는 역할을 합니다.

사용자가 `SYNC`명령을 ***Lead Peer***에게 요청하면 ***Lead Peer***는 ***Base Peer***로 임시 전환 되며 사용자가 요청한 `cluster`에 첨여 한 `Peer`중에서 임시 ***Lead Peer***를 선출 하여 ***Base Peer***와 통신을 하게 됩니다.

***Base Peer***에 있는 Content를 임시 ***Lead Peer***에게 전송하여 `cluster`에 참여한 다른 모든 `peer`에게도 전달이 가능하도록 합니다.
