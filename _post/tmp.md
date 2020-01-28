Docker Content Trust와 Notary



일반적으로 Docker engine을 통해 Docker hub나 사설 레스트리에 접근해서 push/pull 명령을 실행합니다.

Docker의 Content Trust 기능은 데이터 무결성 및 게시자 확인을 위해서 제공되는 기능입니다.

Docker의 Content Trust(이하 DCT)는 Content의 publisher(여기서는 Docker 이미지 배포자에 해당)가 이미지에 서명(Signing)을 할 수 있게 되고, Consumer(Docker 이미지를 pull해서 사용하는 사람에 해당)가 제대로 서명된 이미지인지 확인할 수 있도록 해줍니다. 

Docker Community Engine기준 17.12 버전, Enterprise 버전으로 18.03 버전 이후로 적용되는 기능입니다.



[REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]

각각의 Docker 이미지는 위와 같이 latest, 1.0같은 Tag를 가질 수 있습니다.

DCT는 바로 이 TAG값과 관련이 있습니다. 각 이미지 레지스트리는 publisher가 이미지 태그에 사인하는데에 사용하는 키쌍을 가집니다. publisher는 어떤 이미지에 태그할 지에 대한 재량권을 가집니다.

어떤 이미지 repo에 있는 이미지에 signed된 태그가 있을 수 도 있고 아닐 수 도 있습니다. 따라서 이미지 게시자가 같은 latest태그를 올리더라도 서명 여부에 따라 서로 구별됩니다.

![img](https://docs.docker.com/engine/security/trust/images/tag_signing.png)

이미지 사용자 측면에서는 서명된 이미지만 필터링할 수 있도록 하는 기능이 제공됩니다. Optional이기 때문에 기본적으로는 서명 여부랑 상관없이 모든 이미지가 보입니다.



이미지 태그에 대한 신뢰는 서명 키(Signing Key)를 통해서 이루어집니다. 서명 키는 RSA알고리즘을 이용한 공개/비밀 키쌍으로 이루어져 있고, 이 키쌍은 DCT이용한 명령이 처음 invoke 될 때 생성됩니다.



이 키는 아래와 같은 키 클래스들로 구성됩니다.

이미지 태그에 대한 DCT root offline key

태그에 서명을하는 repo 혹은 tagging key

timestamp키 같이 서버에서 관리되는 key



![Content Trust components](https://docs.docker.com/engine/security/trust/images/trust_components.png)



이 Key구조는 Notary에 의한 것으로, 좀더 정확히는 Notary가 차용한 TUF의 구조를 참조합니다.



docker trust기능은 Notary 기능 기반으로 동작합니다. 따라서 Docker Registry에 Notary 서버가 연결되어 있는 것을 전제로 동작합니다. 우리가 흔히 사용하는 Docker Hub나 Docker Trusted Registry에는 이 Notary 서버가 연결되어 있습니다. 따라서 별도의 Notary 서버를 동작시킬 필요 없다는 점이 Docker Trusted Registry의 장점이 되겠습니다.



notary 설치

sudo snap install docker

git clone https://github.com/theupdateframework/notary.git
cd notary
docker-compose up

입력하게 되면 기본 설정값에 따라 docker notary 서버를 구성하게 됩니다.
default로 db는 mysql을 사용하게 됩니다. 설정값에 따라 다른 DB를 사용할 수 있습니다.

그냥 notary로 사용되지는 않고 docker trust 기반 명령으로 사용하게 됩니다.

docker registry와 연결하려면 별도의 설정이 필요합니다.



사용 방법

먼저 키쌍을 생성합니다.

키쌍을 생성하기 위해 Docker Documentation 상에서 제시하는 방법은 2가지가 있습니다.

1) docker trust key generate [NAME] 명령으로 직접 생성

1)의 경우로 키를 생성하면 자동으로 개인키를 local docker registry에 저장합니다. 기본 위치는 ~/.docker/trust/ 입니다.

2) 인증기관에서 발급된 키나 사전에 만들어진 키 사용

이미 만들어진 키를 registry에 추가합니다.




https://engineering.linecorp.com/ko/blog/harbor-for-private-docker-registry/

1. Docker Enterprise를 사용하는 방법
Docker Trusted Registry 기능 중하나로 Image Signing을 제공합니다.
돈이 들지만 가장 마음 편한 방법일지 모릅니다.

링크-  https://docs.docker.com/ee/dtr

2. private docker registry에 notary를 만들어서 운용하는 방법
기업환경에서 2의 방법으로  private docker registry를 그냥 운용하는 경우는 거의 없기 때문에 다른 오픈소스와의 연동을 생각해보아야 합니다.
현재 사용 중인 harbor의 경우 notary 서버까지 지원하고 있습니다.

Docker hub에서 찾을 수 있는 모든 공식 도커 라이브러리 이미지들은(docker.io/library/*) 같은 root key를 가집니다.

~/.docker/trust/trusted_certificates/docker.io/library/를 확인해보면 이 key를 확인할 수 있습니다.



참조

https://docs.docker.com/engine/security/trust/content_trust/

https://docs.docker.com/engine/security/trust/trust_automation/

https://medium.com/faun/what-docker-notary-doesnt-do-ca65ee4c49cc

https://blog.inkubate.io/how-to-use-harbor-private-registry-with-kubernetes/

https://www.leafcats.com/208
