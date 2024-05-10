# Kubernetes microservices AWS cloud note

## Section 3: Kubernetes

### Orchestration

- 컴퓨터 시스템, 미들웨어, 서비스 등을 자동화하고 조정하는 컴퓨터 소프트
- 컨테이너 오케스트레이션은 컨테이너화된 애플리케이션을 배포, 관리, 확장하는 일련의 프로세스를 자동화하는 것을 의미
- 특정 컨테이너가 항상 실행 중이도록 만들 수 있다.
- 또한 어떠한 에러가 발생하더라도 컨테이너가 다시 시작되도록 할 수 있다.


### Kubernetes(K8S)

- 컨테이너 오케스트레이션 도구 중 하나
- Kubernetes를 위한 [다양한 도구들](https://kubernetes.io/docs/tasks/tools/)이 있다.
    - minikube: 로컬 환경에서 Kubernetes 클러스터를 구축할 수 있는 도구
    - kubectl :Kubernetes 클러스터와 상호작용하기 위한 커맨드 라인 도구

> ### 주의사항(docker가 이미 설치되어 있다면)
> - minikube 내부에 자체 docker 설치가 함께 제공되기에 docker를 설치할 필요가 없다.
> - 만약 이미 docker를 설치했다면 도커 드라이버를 사용하면 된다.
> - minikube 설치를 완료하고 `minikube start --memory 4096 --driver docker` 명령어로 minikube를 실행하면 된다.
> - 또한 docker desktop이 있다면 minukube 말고 docker desktop을 사용해서 진행할 수 있다.


### Kubernetes Cluster

- Kubernetes 클러스터는 애플리케이션 컨테이너를 실행하기 위한 일련의 노드 집합이다.
- Kubernetes를 실행 중이라면 클러스터를 실행하고 있는 것이다.
- 클러스터는 여러 노드로 구성되어 있으며, 각 노드는 Kubernetes의 일부이다.
- 클러스터는 물리 머신, 가상 머신, 온프레미스, 클라우드에 구애받지 않고 머신 그룹 전체에서 컨테이너를 예약하고 실행할 수 있다.
- 쿠버네티스 컨테이너는 개별 머신에 연결되지 않고 클러스터 전체에서 추상화된다.
- 관련 용어(강의를 통해 자세히 학습할 예정)
  - control plane: 쿠버네티스 노드를 제어하는 프로세스의 모음이다. 여기에서 모든 테스크 할당이 시작된다.
  - node: 컨트롤 플레인에서 할당된 요청 태스크를 수행하는 머신이다.
  - pod: 쿠버네티스에서 가장 작은 배포 단위이다. 단일 노드에 배포되는 하나 이상의 컨테이너 집합이다.
  - service: 일련의 포드에서 네트워크 서비스로 실행 중인 애플리케이션을 노출하는 방식이다. pod의 집합에 대한 네트워크 엔드포인트를 정의한다.
  - volume
    - 컨테이너에서 데이터를 저장하는 방법이다. 컨테이너가 종료되면 데이터가 사라지기 때문에 별도의 볼륨을 사용한다.
    - 쿠버네티스 볼륨은 이 볼륨을 묶는 포드의 수명과 동일하다.
    - 볼륨은 포드 내에서 실행되는 모든 컨테이너보다 오래 지속되며 컨테이너를 다시 시작해도 데이터는 보존된다.
  - namespace: 쿠버네티스 클러스터 내의 가상 클러스터이다. 이를 통해 하나의 물리 클러스터 내에 있는 여러 클러스터를 관리할 수 있다.
- [RedHat에서 제공하는 쿠버네티스 클러스터에 대한 간단한 설명](https://www.redhat.com/ko/topics/containers/what-is-a-kubernetes-cluster)


## Section 4: Kubernetes Pods

### Pods

- Kubernetes는 컨테이너의 시작과 종료를 총체적으로 관리한다고 할 수 있다.
- 이를 위해 Kubernetes는 아키텍쳐의 조직을 위해 여러 개념을 가지고 있는데 그 중 가장 기본 개념이 Pod이다.
- 각 컨테이너들에게 Pod를 만들어 일종의 컨테이너 포장지처럼 감싼다. 
- 그렇기에 pod와 컨테이너는 대부분 일대일 관계를 가진다.
    - 예외적으로 컨테이너의 동작을 보조하는 컨테이너를 같은 pod에 둘 수 있다.
    - 이를 **sidecar 컨테이너** 라고 한다.
    - 허나 하나의 pod가 두 개의 마이크로서비스를 갖는 것은 매우 드문 일이다.
- kubernetes는 이 pod를 관리하고, pod가 어떻게 동작하는지를 결정한다. 그렇기에 pod는 kubernetes의 기본으로 배포되는 유닛이다.
- pod는 쿠버네티스 클러스터 외부에서 확인될 수 없다. pod는 클러스터 내부에서만 확인할 수 있다.
- 관련 링크: [Pod 설명](https://kubernetes.io/docs/concepts/workloads/pods/), [Pod API](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/)

### Pod 구성 yaml 파일 예시

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod  # pod의 이름
spec:
containers:  # pod에 포함될 컨테이너 목록
  - name: ubuntu
    image: ubuntu:trusty
    command: ["echo"]
    args: ["Hello World!"]
```

### Pod 명령어

#### 현재 클러스터에 있는 모든 리소스 출력

```bash
kubectl get all
```

#### Pod 생성 및 변경 사항 적용

```bash
 # pod 생성
kubectl apply -f {pod-definition.yaml}
```

#### Pod 목록 출력
  
```bash
kubectl get pods

# 약어를 사용할 수도 있다.
kubectl get po

# label 같이 출력
kubectl get po --show-labels

# label 별로 출력
kubectl get po -l {label-name}
```

#### Pod 상세 정보 출력

```bash
kubectl describe pod {pod-name}
```

#### Pod 명령어 실행

```bash
kubectl exec {pod-name} {command}
```

#### Pod shell 접속

```bash
kubectl exec -it {pod-name} sh
```

#### Pod 삭제
```bash
 # pod 삭제
kubectl delete po {pod-name}

# 전체 삭제
kubectl delete po --all
```

> #### minikube ip 관련
> 
> - 도커 드라이버나 도커 데스크탑을 사용하면 `minikube ip` 명령어가 동작하지 않는다.
>   
> 
> ##### docker desktop 사용 시
> 
> - docker desktop을 사용하면 `localhost`로 접근할 수 있다.
> - 강의에서 주어진 port는 30080 포트이다. 따라서 `localhost:30080`으로 접근하면 된다.
> - 만약 동작하지 않는다면 `kubectl port-forward webapp 30080:80` 을 실행한 뒤, 접속하면 된다.
> - service를 사용하면 위 명령어를 사용하면 안 된다.
> 
> 
> ##### docker driver 사용 시
> 
> - `minikube service fleetman-webapp` 명령어를 통해 접근할 수 있다.
> - 브라우저가 자동으로 열린다.
> - 만약 열리지 않는다면 출력 결과에 있는 url을 보고 접속해야 한다.

## Section 5: Kubernetes Services

### Service

#### Pod의 문제점
- pod는 짧은 수명을 가지기 때문에 pod가 종료되면 새로운 pod가 생성된다.
- 이 때 pod의 IP 주소가 변경되기 때문에 pod를 외부에서 접근할 수 없다.

#### Service
- 이를 해결하기 위해 Service를 사용한다.
- Service는 긴 수명과 IP 주소, 안정되고 고정적인 port를 갖는다.
- Service는 pod의 집합을 대표하며, pod의 집합을 논리적인 그룹으로 묶어 외부와 통신할 수 있게 한다.
- [Service API](https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/)

### Service와 Pod의 관계
- Pod는 Labels(임의의 key:value 값)를 가지고 있다.
- Service는 Selector(임의의 key:value 값)를 가지고 있다.
- Service는 Selector와 일치하는 Pod를 찾아 트래픽을 전달한다.

### Service 구성 yaml 파일 예시

```yaml
kind: Service
apiVersion: v1
metadata:
  # Unique key of the Service instance
  name: service-example  # 서비스 이름
spec:
  ports:
    # Accept traffic sent to port 80
    - name: http
      port: 80
      targetPort: 80
  selector:
    # Loadbalance traffic across Pods matching
    # this label selector.
    app: nginx # pod의 label
  # Create an HA proxy in the cloud provider
  # with an External IP address - "Only supported
  # by some cloud providers"
  type: LoadBalancer
```

- `type: LoadBalancer`는 클라우드 제공자에서만 지원한다.
- 로컬에서 사용하려면 아래 두 개중 하나를 사용하면 된다.
  - `type: ClusterIp`
    - 클러스터 내부에서만 접근 가능하다.
    - 주로 클러스터 내부에서 통신하고 외부 트래픽에 노출시키지 않을 때 사용한다.
  - `type: NodePort`
    - 클러스터 외부에서 접근 가능하다.
    - 서비스와 연결된 포드로 접근할 수 있도록 만든다.
    - 이때는 `ports` 블록 내부에 `nodePort`를 추가해야 한다.
    - `nodePort`는 외부에서 접근할 수 있는 포트를 설정하는 것이며 30000-32767 사이의 포트를 사용한다.

- 또한 service와 pod를 연결하기 위해 pod의 label을 설정해야 한다.

```yaml
...
metadata:
  name: webapp
  labels:
    app: webapp
...
```

- labels에 설정한 `app: webapp`은 service의 selector에 설정한 `app: webapp`과 일치해야 한다.
- labels의 키와 값은 자유롭게 설정할 수 있다.

### Service 명령어

#### Service 생성 및 변경사항 적용

```bash
kubectl apply -f {service-definition.yaml}
```

#### Service 목록 출력

```bash
kubectl get services

# 약어를 사용할 수도 있다.
kubectl get svc

# label 같이 출력
kubectl get svc --show-labels
```

#### Service 상세 정보

```bash
kubectl describe service {service-name}

# 약어를 사용할 수도 있다.
kubectl describe svc {service-name}
```

#### 현재 경로에 있는ㄴ yaml 파일 적용

```bash
kubectl apply -f .
```

### 새로운 버전의 Pod 배포

- 새로운 버전의 pod를 배포하기 위해 기존 pod를 지우고 새로운 pod를 생성하면 생성되는 기간동안 서비스 사용이 불가하다.
- 그렇기 때문에 기존 pod를 열어두고 새로운 pod를 생성한 뒤 서비스를 전환하는 방법을 사용한다.
- 이때 labels를 통해 pod를 전환한다.

```yaml
# first-pod.yaml
...
metadata:
  name: webapp
  labels:
    app: webapp
    release: "0"
...

---

...
metadata:
  name: webapp-release-0-5
  labels:
    app: webapp
    release: 0-5
...
```

기존 포드와 새로운 포드를 위와 같이 파일에 정의한 뒤, labels 값으로 버전을 하나 추가한다.

```yaml
# webapp-service.yaml
...
spec:
  selector:
    app: webapp
    release: "0" # -> "0-5"
...
```

서비스는 기존 포드를 바라보다가 새로운 포드가 배포되면 새로운 포드를 바라보도록 변경하면 된다.
`describe` 명령어를 통해 서비스의 labels를 확인할 수 있다.


## Section 8: Kubernetes ReplicaSets

### Pod의 한계

- 결론부터 말하면 실제 서비스에서는 Pod를 직접 사용하지 않는다.
- 왜냐하면 pod는 다양한 이유로 종료될 수 있기 때문이다.
- 그리고 직접 배포한 pod가 종료된다면 그 pod는 자동적으로 다시 재시작되지 않는다.

### ReplicaSets

- ReplicaSets는 Pod를 관리하는 객체이다.
- ReplicaSets는 Pod의 수를 관리하고, Pod가 종료되면 새로운 Pod를 생성한다.
- [ReplicaSet API](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/replica-set-v1/)
- ReplicaSets의 yaml 파일 내에는 Pod 관련 내용도 포함된다.

### ReplicaSet 구성 yaml 파일 예시

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  # Unique key of the ReplicaSet instance
  name: replicaset-example

spec:
  selector:
    matchLabels:
      app: nginx
  # 3 Pods should exist at all times.
  replicas: 3
  template:  # template for the pods
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
```

- template 내부에 pod의 내용을 작성한다.
- replica의 이름은 pod의 이름과 동일한 것을 사용하는 것이 좋다.
- 또한 pod는 replica의 관리를 받고 있기에 따로 이름을 가질 필요가 없다.
- replica는 동일한 label을 가진 포드를 관리하고 포드가 어떤 이유간에 종료되면 새로운 포드를 실행시킨다.

### ReplicaSet 명령어

- 기존 명령어와 동일하고 `replicaset` 혹은 `rs` 를 사용하면 된다.

### ReplicaSet 내부 pod가 종료될 때

- ReplicaSet는 Pod를 관리하기 때문에 Pod가 종료되면 새로운 Pod를 생성한다.
- 만약 `replicas` 필드의 값이 2라면 같은 pod가 2개 생성된다.
- 그리고 서비스는 네트워크의 엔드포인트, 즉 이 경우엔 방문할 브라우저를 나타내는데 엔드포인트가 두 개 이상의 포드에 서비스되고 요청에 응답하는 포드가 지속적으로 변경된다는 게 쿠버네티스의 동작이다.

### 공식문서의 권장사항

- 공식문서를 보면 대부분의 경우 ReplicaSet 대신 배포, 생성을 권장한다는 경고 문구가 있다.


## Section 9: Kubernetes Deployments

### Deployments(배포)

- 배포는 레플리카 세트를 관리하는 객체이다.
- 쿠버네티스에서 배포는 기본적으로 보다 정교한 형태의 레플리카 세트라고 할 수 있다.
- 레플리카 세트에서 중단 없이 자동 업데이트(롤링 업데이트)를 할 수 있는 기능을 추가한 것과 비슷하다.
- [Deployment API](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/)

### 배포 중 문제가 생겼을 때

- 이미지 이름이 없거나 네트워크 문제가 생겨서 이미지를 가져오지 못할 때는 기존 레플리카 셋은 계속 유지되고 새로운 레플리카 셋은 무한 루프를 돌며 이미지를 가져온다.
- 기존 레플리카 셋이 종료되지 않았기에 서비스는 기존 버전으로 계속해서 실행되고 있는 상태다.
- 그렇기에 문제를 천천히 찾아본 후, 수정하여 적용하면 된다.

### Deployment 구성 yaml 파일 예시

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  # Unique key of the Deployment instance
  name: deployment-example
spec:
  replicas: 3
  template:
    metadata:
      labels:
          app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
```

### 롤링 업데이트

- 새로운 버전으로 업데이트 할 때, 기존 버전이 중단되지 않도록 업데이트 하는 방법이다.
- 과정은 아래와 같다
  1. 새로운 레플리카 셋을 생성한다.
  2. 새로운 레플리카 셋이 준비되면 기존 레플리카 셋을 제거한다.
  3. 이때 기존 레플리카 셋 자체를 삭제하는 것이 아니라 레플리카 수를 0으로 만든다.
  4. 기존 레플리카 셋이 남아있기에 추후 롤백이 가능하다.
- 방법은 yaml 파일에서 버전만 변경하고 `apply` 명령을 실행하면 된다.
- 기존 레플리카 버전은 최대 10까지 보관한다.

### Deployment 명령어

- 기존 명령어와 동일하고 `deployment` 혹은 `deploy` 를 사용하면 된다.

#### Rollout 상태 확인

```bash
kubectl rollout status deployment {deployment-name}
```

#### Rollout 이력 확인

```bash
kubectl rollout history deployment {deployment-name}
```

#### Rollback

```bash
kubectl rollout undo deployment {deployment-name}

# 특정 버전 지정
kubectl rollout undo deployment {deployment-name} --to-revision={revision-number}
```

- Rollback은 유용하지만 롤백 후에 yaml 파일과 동일하지 않은 형태임을 인지해야 한다.
- 그렇기에 롤백 후에는 yaml 파일도 바로 수정해 주어야 한다.
- 그렇기에 긴급할 때(배포 후, 비정상 동작할 때와 같은)만 사용하는 것을 추천한다.

