# vSphere Storage

ステートフルなワークロードにvSphereストレージを提供するために、vSphereVolume[ストレージクラス](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/#storage-classes)を作成することをお勧めします。
このプラクティスは、ワークロードが[永続的なボリューム要求を通じてボリュームを要求](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/persistent-volume-claims/)するときに、vSphereストレージを動的にプロビジョニングします。

## Prerequisites(前提条件)

[Rancher Kubernetes Engine（RKE）](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/)で作成されたクラスターにvSphereボリュームをプロビジョニングするには、[クラスターオプション](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/)で[vSphereクラウドプロバイダー](https://rancher.com/docs/rke/v0.1.x/en/config-options/cloud-providers/vsphere/)を明示的に有効にする必要があります。


---
---
---

以下略

