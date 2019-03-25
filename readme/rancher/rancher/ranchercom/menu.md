# Rancher 2.x

参考URL： [Rancher 2.X](https://rancher.com/docs/rancher/v2.x/en/)

---
## [What's New ?](0000whatnew/0000whatnew.md)
---
## [Overview(概要)](0100overview/0100overview.md)
> ### [Architecture](0100overview/0200architecture.md)
---
## [Quick Start Guides](0200quickstartguides/0100quickstartguides.md)
> ### [Deploying Rancher Server](0200quickstartguides/0200deployingrancherserver.md)
>> #### [Amazon AWS Quick Start](0200quickstartguides/0201amazonAWSquickstart.md)
>> #### [DigitalOcean Quick Start](0200quickstartguides/0202digitalOceanquickstart.md)
>> #### [Vagrant Quick Start](0200quickstartguides/0203Vagrantquickstart.md)
>> #### [Manual Quick Start](0200quickstartguides/0204Manualquickstart.md)
> ### [Deploying Workloads](0200quickstartguides/0300deployingworkloads.md)
>> #### [Workload with Ingress Quick Start](0200quickstartguides/0301Workloadwithingressquickstart.md)
>> #### [Workload with NodePort Quick Start](0200quickstartguides/0302WorkloadwithNodePortquickstart.md)
---
## [Installation](0300installation/0100installation.md)
> ### [Node Requirements](0300installation/0200noderequirements.md) 
> ### [Choosing a Version of Rancher](0300installation/0300choosingversionofrancher.md)
> ### [Single Node Install](0300installation/0400singlenodeinstall.md)
>> #### [HTTP Proxy Configuration](0300installation/0410HTTPproxyconfiguration.md)
>> #### [Single Node Install with External Load Balancer](0300installation/0420installwithloadbalancer.md)
> ### [High Availability (HA) Install](0300installation/0500highavailabilityinstall.md)
>> #### [1. Create Nodes and Load Balancer](0300installation/0510createnodesandloadbalancer.md)
>>> ##### [NGINX](0300installation/0511NGINX.md)
>>> ##### Amazon NLB
>> #### [2. Install Kubernetes with RKE](0300installation/0520installKuberneteswithRKE.md)
>>> ##### [Troubleshooting](0300installation/0521troubleshooting.md)
>> #### [3. Initialize Helm](0300installation/0530initializehelm.md)
>>> ##### [Troubleshooting](0300installation/0531troubleshooting.md)
>> #### [4. Install Rancher](0300installation/0540installrancher.md)
>>> ##### [Adding TLS Secrets](0300installation/0541addingTLSsecrets.md)
>>> ##### [Chart Options](0300installation/0542chartoptions.md)
>>> ##### [Troubleshooting](0300installation/0543troubleshooting.md)
>> #### [RKE Add-On Install](0300installation/0550RKEadd-oninstall.md)
>>> ##### [HA Install with External Load Balancer (TCP/Layer 4)](0300installation/0551HAinstallwithexternalloadbalancer4.md)
>>>> ###### Amazon NLB Configuration
>>> ##### [HA Install with External Load Balancer (HTTPS/Layer 7)](0300installation/0552HAinstallwithexternalloadbalancer7.md)
>>>> ###### Amazon ALB Configuration
>>>> ###### NGINX Configuration
>>> ##### HTTP Proxy Configuration
>>> ##### Enable API Auditing
>>> ##### Troubleshooting HA RKE Add-On Install
>>>> ###### Generic troubleshooting
>>>> ###### Failed to get job complete status
>>>> ###### 404 - default backend
> ### Air Gap: Single Node Install
>> #### 1. Provision Linux Host
>> #### 2. Prepare Private Registry
>> #### 3. Choose an SSL Option and Install Rancher
>> #### 4. Configure Rancher for the Private Registry
> ### Air Gap: High Availability Install
>> #### 1. Create Nodes and Load Balancer
>> #### 2. Prepare Private Registry
>> #### 3. Install Kubernetes with RKE
>> #### 4. Install Rancher
>> #### 5. Configure Rancher for the Private Registry
> ### Port Requirements 
---
## Upgrades and Rollbacks
---
## Backups and Disaster Recovery
---
## Administration
---
## Provisioning Kubernetes Clusters
---
## [Kubernetes in Rancher](0800kubernetesrancher/0100kubernetesrancher.md)
> ### [Horizontal Pod Autoscaler](0800kubernetesrancher/0200horizontalpodautoscaler.md)
> ### [Using kubectl to Access a Cluster](0800kubernetesrancher/0300usingkubectlcluster.md)
> ### [Kubeconfig Files](0800kubernetesrancher/0400kubeconfigfiles.md)
> ### [Editing Clusters](0800kubernetesrancher/0500editingclusters.md)
> ### [Projects and Namespaces](0800kubernetesrancher/0600projectsnamespaces.md)
>> #### [Editing Projects](0800kubernetesrancher/0610editingprojects.md)
>> #### [Adding Users to Projects](0800kubernetesrancher/0620addingusersprojects.md)
>> #### [Resource Quotas](0800kubernetesrancher/0630resourcequotas.md)
> ### [Workloads](0800kubernetesrancher/0700workloads.md)
>> #### [Deploying Workloads](0800kubernetesrancher/0710deployingworkloads.md)
>> #### [Rolling Back Workloads](0800kubernetesrancher/0720rollingbackworkloads.md)
>> #### [Upgrading Workloads](0800kubernetesrancher/0730upgradingworkloads.md)
>> #### [Adding a Sidecar](0800kubernetesrancher/0740addingsidecar.md)
> ### [Load Balancing and Ingresses](0800kubernetesrancher/0800loadbalancingingresses.md)
>> #### [Load Balancers](0800kubernetesrancher/0801loadbalancers.md)
>> #### [Ingress](0800kubernetesrancher/0802ingress.md)
> ### [Service Discovery](0800kubernetesrancher/0900servicediscovery.md)
> ### [Volumes and Storage](0800kubernetesrancher/1000volumesstorage.md)
>> #### [Persistent Volume Claims](0800kubernetesrancher/1010persistentvolumevlaims.md)
>> #### [Provisioning Storage Examples](0800kubernetesrancher/1020provisioningstorageexampl.md)
>>> ##### [NFS Storage](0800kubernetesrancher/1021NFSstorage.md)
>>> ##### vSphere Storage 
> ### [SSL Certificates](0800kubernetesrancher/1100SSLcertificates.md)
> ### [ConfigMaps](0800kubernetesrancher/1200configmaps.md)
> ### [Secrets](0800kubernetesrancher/1300secrets.md)
> ### [Registries](0800kubernetesrancher/1400registries.md)
> ### [Nodes](0800kubernetesrancher/1500nodes.md)
---
## [Catalogs and Charts](0900catalogsandcharts/0100catalogsandcharts.md)
> ### [Custom Catalogs and Charts](0900catalogsandcharts/0200customcatalogscharts.md)
---
## [Rancher Tools](1000ranchertools/0100ranchertools.md)
> ### Pipelines
>> #### Pipelines Quick Start Guide
>> #### Pipeline Terminology
>> #### Configuring Pipelines
>> #### Pipeline Variable Reference
>> #### v2.0.x Pipeline Documentation
> ### Alerts and Notifiers
> ### Logging
>> #### Elasticsearch
>> #### Splunk
>> #### Kafka
>> #### Syslog
---
## Rancher CLI
---
## User Settings
> ### API Keys
> ### Managing Node Templates
> ### User Preferences
---
## API
---
## Rancher Security
---
## FAQ
> ### Installing and Configuring kubectl
> ### Networking
>> #### CNI Providers
> ### Technical
> ### Security
> ### Telemetry
---
## Troubleshooting
> ### Kubernetes components
> ### Kubernetes resources
> ### Networking
> ### DNS
> ### Rancher HA
> ### Imported clusters
---
## Contributing to Rancher
---
## Migrating from Rancher v1.6 to v2.x
> ### Kubernetes Introduction
> ### 1.Get Started
> ### 2.Migrate Your Services
>> #### Migration Tools CLI Reference
> ### 3.Expose Your Services
> ### 4.Configure Health Checks
> ### 5.Schedule Your Services
> ### 6.Service Discovery
> ### 7.Load Balancing
