# Architecture

Service Fabric is an [orchestrator](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-cluster-resource-manager-introduction) of services across a cluster of machines. A Service Fabric cluster is a network-connected set of virtual or physical machines into which your microservices are deployed and managed. A machine or VM that is part of a cluster is called a cluster node. Clusters can scale to thousands of nodes. When a Service is deployed on Service Fabric, the deployment artifacts (Code and Configurations) are deployed to each node on this cluster. The figure below shows a Service Fabric cluster with 5 nodes.

![Alt text](/images/ServiceFabricCluster.PNG)

## Service Fabric System Services
Each node on the cluster comes with System Services which provide the platform capabilities of Service Fabric. These services gives an insight of how Service Fabric run internally.
1.	Cluster Manager Service – Responsible for Cluster Management and application life cycle management. It runs on all the nodes and listens for RESTful http traffic on default port 19080. This service can also be accessed by PowerShell or by .Net Fabric client class via port 19000.
2.	Failover Manager Service – Rebalances the service instances running on the available nodes. If a node in the cluster fails, gets added or removed, the service instance running on that node is deployed into another node by this service.
3.	Naming Service – Resolves service names to a location (endpoint) in the cluster. It is capable of resolving the current network location despite node dynamism or the re-sizing of the cluster.
4.	Fault Analysis Service – This service allows to inject faults and run test scenarios say Chaos Monkey, which will kill services or bring down nodes randomly in a controlled and safe manner to test the robustness of the code.
5.	Image Store Service – Provides storage and distribution of the application binaries. (not available when you install Service Fabric on local machine for development)
6.	Upgrade Service – Upgrades the Service Fabric components on the nodes in cluster. (Available only in Azure.)
The image below shows the cluster on local machine for development environment. Note the localhost in the url.

![Alt text](/images/ServiceFabricLocalCluster.PNG)

## Building Microservices on Service Fabric
Microservices can be developed in many ways, from using the [Service Fabric Programming Models](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-choose-framework)  to deploying [guest executables](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-guest-executables-introduction). By default, Service Fabric deploys and activates these services as processes. Processes provide the highest efficiency (fast, small and portable) but the least isolation. This can cause the [“noisy neighbor”](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-resource-governance) problem. The solution is [Containers](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-containers-overview#what-are-containers).

![Alt text](/images/VMContainerProcess.png)

## Containers on Service Fabric
Service Fabric supports the deployment of Docker containers on Linux a well as Windows. The containers running on Service Fabric is similar to running [guest executables](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-guest-executables-introduction), where the existing application is packaged inside a container. Let’s see how it works.

![Alt text](/images/HowItWorks.gif)

Here in this image it shows a host operating system which can be either Linux or Windows having Service Fabric installed on it. It also has [Docker Daemon](https://docs.docker.com/engine/docker-overview/#the-docker-daemon) as well as the [Docker Client](https://docs.docker.com/engine/docker-overview/#the-docker-client) installed on it along with a local repository listening on ports 2376 & 2376. The Service Fabric will read the configuration file [(Service Manifest)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-and-service-manifests#describe-a-service-in-servicemanifestxml) and invoke the Docker Client, which in turn will issue the command to Docker Daemon. The daemon will look into the local repository to see if this version of image “App:v1” is available. If not, it will download the image from the image repository (in this case, it’s the Docker Hub) to the local repository. It will now run the image “App:v1”. The instance of this image which has all the binaries and exes will now run as a container. Service Fabric acts as an orchestrator and offers the following capabilities for containerized services:
1.	Resource governance
2.	Container port to host port mapping
3.	Container to container discovery and communication
4.	DNS service within cluster to resolve container endpoints
5.	Container Image deployment and activation