# Kubernetes practice

## Orchestration

- 컴퓨터 시스템, 미들웨어, 서비스 등을 자동화하고 조정하는 컴퓨터 소프트
- 컨테이너 오케스트레이션은 컨테이너화된 애플리케이션을 배포, 관리, 확장하는 일련의 프로세스를 자동화하는 것을 의미
- 특정 컨테이너가 항상 실행 중이도록 만들 수 있다.
- 또한 어떠한 에러가 발생하더라도 컨테이너가 다시 시작되도록 할 수 있다.


## Kubernetes(K8S)

- 컨테이너 오케스트레이션 도구 중 하나
- Kubernetes를 위한 [다양한 도구들](https://kubernetes.io/docs/tasks/tools/)이 있다.
    - minikube: 로컬 환경에서 Kubernetes 클러스터를 구축할 수 있는 도구
    - kubectl :Kubernetes 클러스터와 상호작용하기 위한 커맨드 라인 도구

> ### 주의사항(docker가 이미 설치되어 있다면)
> - minikube 내부에 자체 docker 설치가 함께 제공되기에 docker를 설치할 필요가 없다.
> - 만약 이미 docker를 설치했다면 도커 드라이버를 사용하면 된다.
> - minikube 설치를 완료하고 `minikube start --memory 4096 --driver docker` 명령어로 minikube를 실행하면 된다.
> - 또한 docker desktop이 있다면 minukube 말고 docker desktop을 사용해서 진행할 수 있다.

