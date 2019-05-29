# Service Fabric as Container Orchestrator

## Overview
Recently Microsoft announced that Service Fabric has been open sourced. You can read about this more [here](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) 

Service Fabric is an open source platform provided by Microsoft which focuses on microservice architecture. Services developed on this platform can take the advantages of the platform like scaling, self-healing, rolling upgrades and are highly available. The platform can be run either on Windows or on Linux and the services can be built using C#, Node, Java, etc. This is just not it, the platform can be run On-Premise, Azure, Amazon or any Cloud Provider of your choice.
![Alt text](/images/MicroservicePlatform.PNG)

## Architecture, Definitions & Concepts

* [Service Fabric Architecture](/architecture/ArchitectureReadme.md)

## Installation of the Service Fabric Cluster

* [Set up development environment on work station](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)
* [Set up unsecured one-machine-three-node local development cluster](/installation/UnsecuredDevClusterReadMe.md)

## Package a Service Fabric application

* [Existing .Net Core application as Guest Executable](/package/GuestExecutableReadMe.md)
* [Existing NodeJS application as Guest Container](/package/GuestContainerNodeJSReadMe.md)

## Deploy application to Service Fabric Cluster

* [Deploy an application package using Powershell](/deployment/PSDeployReadMe.md)
* [Deploy an application package using Visual Studio IDE](/deployment/VSDeployReadMe.md)

## Containers on Service Fabric

* [Windows - Container version compatibility](/containers/WindowsContainersCompatibilityReadMe.md)
