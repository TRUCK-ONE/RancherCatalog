# Rancher 2.x

参考URL： [Rancher 2.X](https://rancher.com/docs/rancher/v2.x/en/)

---
## [Overview](0100overview/0100overview.md)
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
## Installation
> ### Node Requirements 
> ### Choosing a Version of Rancher
> ### Single Node Install
>> #### HTTP Proxy Configuration
>> #### Single Node Install with External Load Balancer
> ### High Availability (HA) Install
>> #### 1. Create Nodes and Load Balancer
>>> ##### NGINX 
>>> ##### Amazon NLB
>> #### 2. Install Kubernetes with RKE
>>> ##### Troubleshooting
>> #### 3. Initialize Helm (Install Tiller)
>>> ##### Troubleshooting
>> #### 4. Install Rancher
>>> ##### Adding TLS Secrets
>>> ##### Chart Options
>>> ##### Troubleshooting
>> #### RKE Add-On Install
>>> ##### HA Install with External Load Balancer (TCP/Layer 4)
>>>> ###### Amazon NLB Configuration
>>> ##### HA Install with External Load Balancer (HTTPS/Layer 7)
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


