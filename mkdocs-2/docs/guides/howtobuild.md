# How to build

***Build 안내서***

----

### Linux

!!! note ""
    go build -tags LINUX

!!! note "서비스 등록"
    cd shell    
    hypercluster.service 파일 적당히 수정    
    chmod +x install_service.sh    
    ./install_service.sh

----

### Windows release

!!! note ""
    go build -tags WINDOWS,RELEASE

!!! note "서비스 등록"
    sc create "HyperCluster" binPath={output}
`{output}` 빌드 결과 파일 경로입니다


!!! note "서비스 삭제"
    sc delete "HyperCluster"  

### Windows debug

!!! note ""
    go build

----    