Kubernetes runs your workload by placing containers into Pods to run on Nodes. A node may be a virtual or physical machine, depending on the cluster. Each node is managed by the control plane and contains the services necessary to run Pods.
kubernates는 너의 workload를 실행한다. 컨테이너를 파드에 배치함으로써 nodes에서 실행시키기 위해. 노드는 아마 가상 혹은 물리적인 머신이다, 클러스터에 의존하고 있는
각각의 노드는 컨트롤 플랜에 의해 관리받는다. 그리고 파드 실행에 필수적인 서비스들을 포함한다.

Typically you have several nodes in a cluster; in a learning or resource-limited environment, you might have only one node.
일반적으로 너는 몇몇의 노드들을 클러스터에서 가지고 있다; 학습 혹은 리소스가 제한적인 환경에서, 너는 아마 오직 하나의 노드만 가지고 있다.

The components on a node include the kubelet, a container runtime, and the kube-proxy.
노드에서 구성요소들은 kubelet,컨테이너 수행시간에, 그리고 kube-proxy를 포함한다.

## Management
There are two main ways to have Nodes added to the API server:

1. The kubelet on a node self-registers to the control plane
2. You (or another human user) manually add a Node object
API서버에 추가되는 노드들을 가지기 위한 두가지 방법이 있다.
1. kubelet이 노드에서 스스로 등록됨 control plane을 위해
2. 너(혹은 다른 사용자)가 노드 오브젝트를 절차적으로 추가한다.

After you create a Node object, or the kubelet on a node self-registers, the control plane checks whether the new Node object is valid. For example, if you try to create a Node from the following JSON manifest:
너가 노드 오브젝트를 생성한후, 혹은 kubelt 노드에서 자체등록, 그 control plane은 확인한다 새로운 노드 오브젝트가 유효한지. 예를 들어, 만약 너가 아래의 JSON manifest로부터 노드를 생성하기 위해 시도한다면

```
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.240.79.157",
    "labels": {
      "name": "my-first-k8s-node"
    }
  }
}
```
Kubernetes creates a Node object internally (the representation). Kubernetes checks that a kubelet has registered to the API server that matches the metadata.name field of the Node. If the node is healthy (i.e. all necessary services are running), then it is eligible to run a Pod. Otherwise, that node is ignored for any cluster activity until it becomes healthy.
쿠버네티스는 생성한다 노드 오브젝트를 내부적으로(표현(?)). 쿠버네티스는 kubelet이 노드의 metadata.name 필드와 일치하는 API 서버에 등록되었는지 확인한다.
만약 노드가 건강하다면(모든 필수적인 서비스들이 실행중), 그렇다면 노드는 파드를 실행시킬 수 있다.
그렇지 않으면, 노드는 무시된다. 어떤 클러스터 활동에대해 이것이 건강해질때까지
*eligible : ...을 가질(할) 수 있는
*Otherwise : 그렇지 않으면

Note:
Kubernetes keeps the object for the invalid Node and continues checking to see whether it becomes healthy.
쿠버네티스는 유지한다. 오브젝트를 유효하지않은 노드에 대해 그리고 지속한다 확인을 보기위해 이것이 건강해지는지 아닌지

You, or a controller, must explicitly delete the Node object to stop that health checking.
당신, 혹은 컨트롤러, 반드시 명시적으로 삭제해야한다. 그 노드 오브젝트를 헬스 체크를 중단하기위해. 
*explicitly : 명시적으로

The name of a Node object must be a valid DNS subdomain name.
노드 오브젝트의 이름은 반드시 유효한 DNS 하위 도메인 이름이어야 한다.

Node name uniqueness

The name identifies a Node. Two Nodes cannot have the same name at the same time. Kubernetes also assumes that a resource with the same name is the same object. In case of a Node, it is implicitly assumed that an instance using the same name will have the same state (e.g. network settings, root disk contents) and attributes like node labels. This may lead to inconsistencies if an instance was modified without changing its name. If the Node needs to be replaced or updated significantly, the existing Node object needs to be removed from API server first and re-added after the update.

이름은 노드를 식별한다. 두개의 노드들은 같은 이름을 가질 수 없다. 동시간에. 쿠버네티스는 또한 추정한다. 같은 이름을 가진리소스는 같은 오브젝트라고. 노드의 케이스에서, 이건은 암시적으로 추정한다. 같은 이름을 사용하는 인스턴스는 같은 상태를 가질것이라고(예를들어, 네트워크 세팅, 루트 디스크 구성) 그리고 특성이 노드라벨과 같을 것이라고. 이것은 아마 이끈다. 불일치로 만약 인스턴스가 수정되었다면 이것의 이름의 변경없이. 만약 그 노드가 중요하게 교체 혹은 업데이트가 필요하다면, 존재하는 노드 오브젝트는 지워질 필요가 있다. API서버로 부터 처음에 그리고 재등록된다. 업데이트 이후에
*assume : 추정하다.
*attributes : 속성, 특성
*inconsistencies : 불일치, 모순
*significantly : 상당히, 중요하게, 의미심장하게