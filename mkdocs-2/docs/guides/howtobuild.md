# How to build

***Build 안내서***

----

### Linux

!!! note ""
    go build -tags LINUX   
    OR    
    chmod +x build_linux_release.sh    
    ./build_linux_release.sh

!!! note "서비스 등록"
    cd build/linux    
    hypercluster.service 파일 적당히 수정    
    chmod +x install_service.sh    
    ./install_service.sh

----

### Windows release

!!! note ""
    go build -tags WINDOWS,RELEASE    
    OR    
    build_win_release.bat

!!! note "서비스 등록"
    sc create "HyperCluster" binPath={hypercluster.exe}     
    sc description "HyperCluster" "Hyper Cluster API Server 0.6.0.0"

`{hypercluster.exe}`는 build를 후 생성된 파일경로 와 파일이름으로 변경.


!!! note "서비스 삭제"
    sc delete "HyperCluster"  

### Windows debug

!!! note ""
    go build   
    OR    
    build_win_debug.bat

----    