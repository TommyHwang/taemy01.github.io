Service



 Pod의 경우 Cluster내에서 동적으로 생성 및 제거 되기 때문에 고정된 IP를 가지지 않는다. 따라서 Pod IP를 기준으로 트래픽을 송신하게 되면 항상 IP가 변경되기 때문에 매번 Pod IP를 새로 알아야하는 문제가 있다. 따라서 Kubernetes 에서는 Service라는 오브젝트를 두어 Pod의 IP를 알 필요 없이 Service로 트래픽을 전달하면 Pod에 트래픽이 전달되도록 구성할 수 있다.

 Service는 Pod와 같이 Yaml로 생성하는 오브젝트다.



Service 를 생성하게 되면   Label확인 후 Endpoint 생성

Master의 Endpoint Controller에 의해 Endpoint가 관리된다. 

엔드포인트 컨트롤러는 api 서버를 통해서 서비스와 파드 리소스를 감시하고 변경사항이 발생할 경우 api 서버에 요청해서 엔드포인드는 생성, 수정, 삭제하는 역할을 한다.



![image-20200406194827185](C:\Users\t1205.hwang\AppData\Roaming\Typora\typora-user-images\image-20200406194827185.png)



![image-20200406194838689](C:\Users\t1205.hwang\AppData\Roaming\Typora\typora-user-images\image-20200406194838689.png)



![image-20200406194903032](C:\Users\t1205.hwang\AppData\Roaming\Typora\typora-user-images\image-20200406194903032.png)

Iptables는 Firewall 설정 및 효과적인 패킷 필터링을 목적으로 만들어진 커널 모듈 O(n)

IPVS는 Load Balancing를 목적으로 만들어진 커널 모듈 O(1)



Kubelet

netshell에서 Kubelet은 노드에서 실행되는 모든 것에 책임을 지는 컴포넌트다.

Init 작업은 노드리소스를 생성해서 실행하고 있는 노드를 등록하는 것이다.

 Api Server를 통해 노드에 스케쥴링 된 Pod에 대한 모니터링을 실시한다.

Master로부터 전달받은 Pod의 스펙에 맞게 실제로 Pod의 컨테이너를 실행시킨다.

컨테이너 모니터링 및 상태, 이벤트, 리소스 소모를 API 서버에 보고

Container 라이브니스 프로브를 실행하고 프로브에 오류 발생 시 컨테이너 재시작

혹은 로컬 매니페스트 파일 참조해서 정적 Pod 실행



https://www.tigera.io/blog/comparing-kube-proxy-modes-iptables-or-ipvs/
