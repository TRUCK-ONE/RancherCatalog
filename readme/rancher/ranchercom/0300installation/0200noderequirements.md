## Node Requirements(Node要件)

Rancherをシングルノードまたは高可用性セットアップのどちらで実行するように構成する場合でも、Rancher Serverを実行する各ノードは次の要件を満たす必要があります。

---
### Operating Systems and Docker

Rancherは、サポートされているバージョンの[Docker](https://www.docker.com/)を使用して、以下のオペレーティングシステムおよびそれ以降の非メジャーリリースでサポートされています。

- Ubuntu 16.04 (64-bit)
    - Docker 17.03.2, 18.06.2, 18.09.2
- Red Hat Enterprise Linux (RHEL)/CentOS 7.5 (64-bit)
    - RHEL Docker 1.13
    - Docker 17.03.2, 18.06.2, 18.09.2
- RancherOS 1.5.1 (64-bit)
    - Docker 17.03.2, 18.06.2, 18.09.2
- Windows Server version 1803 (64-bit)
    - Docker 17.06

RancherOSを使用している場合は、必ずDockerエンジンをサポートされているバージョンに切り替えてください。
```
sudo ros engine switch docker-17.03.2-ce
```
---
### Hardware

ハードウェア要件は、Rancher展開の規模に基づいて調整されます。 要件に従って各ノードをプロビジョニングします。

| Deployment Size | Clusters | Nodes | vCPUs | RAM |
| --- | --- | --- | --- | --- |
| Small | Up to 5 | Up to 50 | 4 | 16GB |  
| Medium | Up to 100 | Up to 500 | 8 | 32GB |  
| Large | Over 100 | Over 500 | Contact Rancher  |  

---
### Networking

#### Node IP address

使用される各ノード（単一ノードインストール、高可用性（HA）インストール、またはクラスタで使用されるノードのいずれか）には、静的IPを構成する必要があります。
DHCPの場合は、ノードに同じIPが割り当てられていることを確認するために、ノードにDHCP予約が必要です。

#### Port requirements

HAクラスタにRancherを配置するときは、Rancherと通信できるように、ノード上の特定のポートを開く必要があります。
開く必要があるポートは、クラスタノードをホストしているマシンの種類によって異なります。
たとえば、インフラストラクチャでホストされているノードにRancherを展開している場合、SSH用にポート22を開く必要があります。
次の図は、各クラスタタイプに対して開かれているポートを示しています。

![画像](../pictures/060001port-communications.svg)


Rancher nodes:
Nodes running the `rancher/rancher` container

#### Rancher nodes - Inbound rules

| Protocol | Port | Source | Description |
| --- | --- | --- | --- |
| TCP | 80 | - Load balancer/proxy that does external SSL termination | Rancher UI/API when external SSL termination is used |
| TCP | 443 | - etcd nodes  - controlplane nodes   


---

---

---







---
[Docker Documentation: インストール手順](https://docs.docker.com/)

