# BackendStudy

```.txt
Kubernetes와 Docker를 함께 사용하기 위해 공부해야 할 핵심 개념들을 정리해 드릴게요.
Docker 기초 개념
컨테이너화 기본 이해가 가장 중요합니다. Dockerfile 작성법, 이미지 빌드, 컨테이너 실행, 네트워킹, 볼륨 마운트 등을 익혀야 합니다. 특히 멀티스테이지 빌드와 이미지 최적화 기법도 중요하죠.
레지스트리 관리도 필수입니다. Docker Hub, ECR, GCR 등에서 이미지를 푸시하고 풀하는 방법, 태깅 전략, 보안 스캔 등을 알아야 합니다.
Kubernetes 핵심 개념
기본 오브젝트들부터 시작하세요. Pod, Deployment, Service, ConfigMap, Secret, Namespace 등이 어떻게 작동하는지 이해해야 합니다. 특히 Pod는 컨테이너를 실행하는 가장 작은 단위이므로 확실히 익혀두세요.
네트워킹은 복잡하지만 중요합니다. 클러스터 내부 통신, 서비스 디스커버리, 인그레스 컨트롤러, 네트워크 정책 등을 공부하세요.
스토리지도 중요한 영역입니다. PersistentVolume, PersistentVolumeClaim, StorageClass를 이해하고, 상태가 있는 애플리케이션을 어떻게 관리할지 알아야 합니다.
실제 운영을 위한 고급 개념
리소스 관리는 필수입니다. CPU, 메모리 requests/limits 설정, HPA(Horizontal Pod Autoscaler), VPA(Vertical Pod Autoscaler) 등을 배우세요.
보안도 중요합니다. RBAC, SecurityContext, Network Policies, Pod Security Standards 등을 이해해야 합니다.
모니터링과 로깅을 위해 Prometheus, Grafana, ELK 스택 등의 도구들도 익혀두면 좋습니다.
추천 학습 순서

Docker 기초 - 컨테이너 개념부터 Dockerfile 작성까지
Kubernetes 아키텍처 이해 - 마스터 노드, 워커 노드, etcd 등
기본 오브젝트 실습 - kubectl로 Pod, Deployment 등 생성/관리
네트워킹과 서비스 디스커버리
스토리지와 상태 관리
헬름(Helm) 패키지 매니저
운영 도구들 - 모니터링, 로깅, CI/CD

실습 환경은 minikube나 kind로 로컬에서 시작하시고, 나중에 EKS, GKE, AKS 같은 관리형 서비스로 확장하시면 됩니다.

Docker 기초부터 차근차근 시작해보겠습니다!
컨테이너 개념 이해
컨테이너란 무엇인가요? 가상머신과 달리 OS 커널을 공유하면서 애플리케이션을 격리된 환경에서 실행하는 기술입니다. 가볍고 빠르며 일관된 실행 환경을 제공합니다.
Docker의 핵심 구성요소를 이해해야 합니다:

이미지(Image): 컨테이너 실행에 필요한 파일과 설정을 담은 템플릿
컨테이너(Container): 이미지를 실행한 인스턴스
레지스트리(Registry): 이미지를 저장하고 공유하는 저장소

Docker 기본 명령어
먼저 이런 명령어들을 익혀보세요:
```
```.py
# 이미지 다운로드
docker pull nginx

# 컨테이너 실행
docker run -d -p 8080:80 --name my-nginx nginx

# 실행 중인 컨테이너 확인
docker ps

# 컨테이너 로그 확인
docker logs my-nginx

# 컨테이너 내부 접속
docker exec -it my-nginx bash

# 컨테이너 중지/시작/삭제
docker stop my-nginx
docker start my-nginx
docker rm my-nginx
```
