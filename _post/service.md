Kubernetes Service는 어떻게 동작하는걸까?

Pod의 경우 Cluster내에서 동적으로 생성 및 제거 되기 때문에 고정된 IP를 가지지 않는다. 따라서 Pod IP를 기준으로 트래픽을 송신하게 되면 항상 IP가 변경되기 때문에 매번 Pod IP를 새로 알아야하는 문제가 있다. 따라서 Kubernetes 에서는 Service라는 오브젝트를 두어 Pod의 IP를 알 필요 없이 Service로 트래픽을 전달하면 Pod에 트래픽이 전달되도록 구성할 수 있다.

Service는 Pod와 같이 Yaml로 생성하는 오브젝트입니다.

Service는 논리적인 개념이기 때문에 Pod내 컨테이너 같이 실제로 프로세스로 동작하지는 않습니다. 

Service Spec에 기재된 LabelSelector를 참고해서 트래픽을 전달받은 Pod를 선택합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: http
  name: httpbin
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: http
```

위의 YAML파일은 Service 예제파일입니다.

spec 아래의 selector를 보면 app: http라고 key/value 쌍이 적혀있습니다.



```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpbin
  labels:
    app: http
spec:
  containers:
  - image: docker.io/honester/httpbin:latest
    imagePullPolicy: IfNotPresent
    name: httpbin
    ports:
    - containerPort: 8080
```

위의 Pod는 



Service 자세한 사항은 아래 링크에서 Kubernetes Docs를 참고하시기 바랍니다.

[Kubernetes Service](https://kubernetes.io/ko/docs/concepts/services-networking/service/)

[Pod와 Service 연결(Connect Application Service)](https://kubernetes.io/ko/docs/concepts/services-networking/connect-applications-service/)





Service는 어떻게 동작할까



쿠버네티스 공신 문서를 읽고 실제로 minikube 등등을 사용하시면서 Service라는 것이 있고 'Service를 사용하게 되면 Pod에 트래픽을 보낼 수 있다' 정도로는 대부분 잘 아시겠지만 실제로 이 Service가 어떻게 동작하길래 Service를 생성하면 Pod로 패킷이 전달되는 건지 의문 사항이 생깁니다.



앞에서 말씀드린 것처럼 Service는 논리적인 개념이기 때문에 프로세스가 존재하는 것이 아닙니다. Service를 생성하게 되면 그저 etcd에 해당 스펙이 저장될 뿐입니다.

 실제로 Pod가 외부에 노출되기 위해서는 1) Service에 LabelSelector에 해당되는 Pod를 찾고, 2) 해당 Pod에 트래픽이 전달되도록 경로 설정 하는 작업이 필요합니다.



1)에 해당하는 작업을 하는 것이 Endpoint controller고, 2)에 해당하는 작업을 하는 것이 kube-proxy입니다.



endpoint controller



kube-proxy와 iptables





Iptables는 Firewall 설정 및 효과적인 패킷 필터링을 목적으로 만들어진 커널 모듈 O(n)

IPVS는 Load Balancing를 목적으로 만들어진 커널 모듈 O(1)



https://www.tigera.io/blog/comparing-kube-proxy-modes-iptables-or-ipvs/
