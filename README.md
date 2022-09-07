# ApplicationSet-on-Red-Hat-Advanced-Cluster-Management

### 1. ApplicationSet

`ApplicationSet`은 GitOps Operator가 지원하는 ArgoCD의 하위 프로젝트입니다. `ApplicationSet`은 Argo CD 애플리케이션에 대한 다중 클러스터 지원을 추가합니다.  Red Hat Advanced Cluster Management 콘솔에서 `ApplicationSet`을 생성할 수 있습니다.



OpenShift Container Platform GitOps는 ArgoCD를 사용하여 클러스터 리소스를 유지 관리합니다. ArgoCD는 애플리케이션의 지속적 통합 및 지속적 배포(CI/CD)를 위한 오픈소스 선언적 도구입니다. OpenShift Container Platform GitOps는 ArgoCD를 컨트롤러(OpenShift Container Platform GitOps Operator)로 구현하여 Git 저장소에 정의된 애플리케이션 정의 및 구성을 지속적으로 모니터링합니다. 그런 다음 ArgoCD는 이러한 구성의 지정된 상태를 클러스터의 현재 상태와 비교합니다.



`ApplicationSet` 컨트롤러는 GitOps Operator 인스턴스를 통해 클러스터에 설치되며 클러스터 관리자 중심 시나리오를 지원하는 추가 기능을 추가하여 이를 보완합니다. `ApplicationSet` 컨트롤러는 다음 기능을 제공합니다.

- 단일 Kubernetes 매니페스트를 사용하여 GitOps Operator로 여러 Kubernetes 클러스터를 대상으로 하는 기능
- 단일 Kubernetes 매니페스트를 사용하여 GitOps Operator를 통해 하나 이상의 Git 리포지토리에서 여러 애플리케이션을 배포하는 기능
- 단일 Git 리포지토리 내에 정의된 여러 ArgoCD 애플리케이션 리소스인 ArgoCD의 컨텍스트에서 monorepo에 대한 지원이 향상되었습니다.
- 다중 테넌트 클러스터 내에서 대상 클러스터/네임스페이스를 활성화 할 때 권한 있는 클러스터 관리자를 포함할 필요 없이 ArgoCD를 사용하여 애플리케이션을 배포할 수 있는 개별 클러스터 테넌트의 기능이 향상되었습니다.



`ApplicationSet` Operator는 클러스터 결정 생성기를 활용하여 사용자 지정 리소스별 로직을 사용하여 배포할 관리 클러스터를 결정하는 Kubernetes 사용자 지정 리소스를 인터페이스 합니다. 클러스터 결정 리소스는 관리되는 클러스터 목록을 생성한 다음 `ApplicationSet` 리소스의 템플릿 필드로 렌더링됩니다. 이것은 참조된 Kubernetes 리소스의 전체 형태에 대한 지식이 필요하지 않은 duck-typing을 사용하여 수행됩니다.



### 2. Configuring Managed Clusters for OpenShift GitOps Operator

GitOps를 구성하기 위해 Red Hat OpenShift Container Platform GitOps Operator 인스턴스에 RHACM에서 관리되는 클러스터를 하나 이상의 세트로 등록할 수 있습니다. 등록한 후 해당 클러스터에 애플리케이션을 배포할 수 있습니다. 지속적인 GitOps 환경을 설정하여 개발, 스테이징 및 프로덕션 환경의 클러스터에서 애플리케이션 일관성을 자동화합니다.

### 2.1 Prerequisites

- RHACM(Red Hat Advanced Cluster Management)에 OpenShift GitOps Operator를 설치해야 합니다.
- 하나 이상의 관리형 클러스터를 가져옵니다.

### 2.2 Registering managed clusters to GitOps

- 관리형 클러스터 세트를 생성하고 관리형 클러스터를 해당 관리형 클러스터 세트에 추가합니다.

- **RHACM 콘솔 접속 > Infrastructure > Clusters > Cluster sets > Create cluster set**

  ![01_cluster_set](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/01_cluster_set.png)

  - Cluster set name 입력

    ![02_create_cluster_set](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/02_create_cluster_set.png)

- Manage resource assignments 선택

  위에서 생성한 Cluster  sets가 생성되면 `Managed resource assignments`를 선택합니다.

  ![03_manage_resource_assignments](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/03_manage_resource_assignments.png)

  대상 클러스터를 선택합니다.

  ![04_select_resources_cluster_set](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/04_select_resources_cluster_set.png)

- GitOps Operator가 배포된 네임스페이스에 대한 관리형 클러스터 세트 바인딩을 생성합니다.

  - **RHACM 콘솔 > Infrastructure > Clusters > Cluster sets > 생성한 Cluster set > Actions > Edit namespace bindings**

    ![05_edit_namespace_bindings01](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/05_edit_namespace_bindings01.png)

  - Namespaces : GitOps Operator Instance가 구성된 네임스페이스 (`openshift-gitops`)

    ![06_edit_namespace_bindings02](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/06_edit_namespace_bindings02.png)

- ManagedClusterSet에 사용자 또는 그룹 역할 기반 액세스 제어 권한 할당(RBAC)

  - **ACM 콘솔 접속 > Clusters > Cluster set > `acm-argoset` > Access management > Add User or group > Select User : `admin` > Select role : `Cluster set admin`**
  
    ![07_access_management](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/07_access_management.png)
  
  - 생성 확인
  
    ![08_confirm_access_management](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/08_confirm_access_management.png)

- ManagedClusterSet binding에 사용되는 네임스페이스에서 사용자 지정 리소스를 생성하여 OpenShift GitOps Operator 인스턴스에 등록할 배치 사용자 리소스 생성

  ==ACM에서 GitOps Operator Instance로 등록해서 사용할 리소스==

  ```yaml
  cat << EOF > managedcluster-argoset.yaml
  apiVersion: cluster.open-cluster-management.io/v1beta1
  kind: Placement
  metadata:
    name: acm-argoset
    namespace: openshift-gitops
  spec:
    predicates:
    - requiredClusterSelector:
        labelSelector:
          matchExpressions:
          - key: vendor
            operator: "In"
            values:
            - OpenShift
  EOF
  
  oc apply -f managedcluster-argoset.yaml
  ```

- `GitOpsCluster` 배치 결정에서 지정된 인스턴스까지 관리되는 ClusterSet을 등록하기 위한 사용자 정의 리소스 생성

  ```yaml
  cat << EOF > gitops-cluster.yaml
  apiVersion: apps.open-cluster-management.io/v1beta1
  kind: GitOpsCluster
  metadata:
    name: gitops-cluster
    namespace: openshift-gitops
  spec:
    argoServer:
      cluster: local-cluster  
      argoNamespace: openshift-gitops
    placementRef:
      kind: Placement
      apiVersion: cluster.open-cluster-management.io/v1beta1
      name: acm-argoset
      namespace: openshift-gitops    
  EOF
  
  oc apply -f gitops-cluster.yaml
  ```
  > 참고)
  >
  > spec 부분
  >
  > - argoserver > cluster :  이 부분이 gitOps 인스턴스가 설치된 클러스터를 지정!! 
  >   - placementRef > name: GitOps 인스턴스에서 관리될 클러스터 (acm-argoset)

- `local cluster`의 GitOps 인스턴스 콘솔로 접속하여 새로 추가된 클러스터(`dev-cluster`) 정보 확인

  ![09_argocd_cluster](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/09_argocd_cluster.png)

### 3. Configure ArgoCD RBAC on OpenShift

### 3.1 Configure ArgoCD RBAC

기본적으로 RHSSO를 사용하여 ArgoCD에 로그인 한 경우에는 읽기 전용(read-only) 사용자입니다. 사용자 수준의 액세스를 변경하고 관리 할 수 있습니다.

**3.1.1) 현재 설정 확인**

- CLI 명령어로 확인 (편집 모드)

  ```bash
  oc edit cm argocd-rbac-cm -n openshift-gitops
  ```

- 기본값 : readonly

  ```yaml
  ...
  metadata
  ...
  ...
  data:
    policy.default: role:readonly
  ```

- Conole에서 확인

  - **`openshift-gitops` 프로젝트 > Workloads > ConfigMap > argocd-rbac-cm > data 확인**

    ![01_rbac_policy](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/17_rbac_policy.png)

- `admin` 권한으로 변경 (CLI 명령어로 적용하는 경우)

  ```bash
  oc patch cm/argocd-rbac-cm -n openshift-gitops --type=merge -p '{"data":{"policy.default":"role:admin"}}'
  ```

- Console에서 변경하는 방법

  - **`openshift-gitops`  프로젝트 > Workloads > ConfigMap > argocd-rbac-cm > Edit ConfigMap > Save**

  - `role:readonly` -> `role:admin`

    ![02_rbac_cm_update](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/18_rbac_cm_update.png)

  - 적용 확인

    ![03_rbac_policy_confirm](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/19_rbac_policy_confirm.png)

    > policy가 `admin`으로 수정된 이후에는 openshift 계정으로 로그인하여 ArgoCD 인스턴스에서 리소스를 생성 할 수 있습니다.

  - LOG IN VIA OPENSHFIT

    ![04_argocd_login_openshift](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/20_argocd_login_openshift.png)

### 4. Deploying ApplicationSet 

ApplicaionSet을 이용하여 RHACS에서 대상 클러스터에 애플리케이션을 배포합니다.

### 4.1 Deploying ApplicationSet

- **ACM 콘솔 접속 > Applications > Create application > ApplicationSet**

  - **General**

    ![10_applicationset_general](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/10_applicationset_general.png)

    - ApplicationSet name : dev-bgd
    - Argo server: openshift-gitops
    - Requeue time: 180 (default)

  - **Template**

    ![11_applicationset_template](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/11_applicationset_template.png)

    - Source
      - Resource Type : Git
      - URL : ${GIT_REPOSITORY}
      - Revision : main
    - Destination
      - Remote namespace : dev-bgd

  - **Source Policy (기본값으로 두고 진행)**

    ![12_applicationset_sync_policy](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/12_applicationset_sync_policy.png)

  - **Placement**

    ![13_applicationset_placement](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/13_applicationset_placement.png)

    - Cluster sets : `acm-argoset` (위에서 생성한 대상 클러스터 세트 선택)

  - **Review**

    ![14_applicationset_review](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/14_applicationset_review.png)

  - **Topology 확인 (ACM Console)**

    ![15_application_topology](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/15_application_topology.png)

  - **ArgoCD 콘솔 확인**

    ![16_argocd_console](https://github.com/justone0127/ApplicationSet-on-Red-Hat-Advanced-Cluster-Management/blob/main/images/16_argocd_console.png)
