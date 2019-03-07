# Windows Container version compatibility
A container built using microsft/nanonserver latest image **works** on Windows server 2016 version 1607 (OS build 14393.2759) but has **compatibility issues** with Windows Server 2019 version 1809 (OS Build 17763.107). Windows Server Containers are blocked from starting when the **build number** between the container host and the container image are different. In this case, see the table below.

![Alt text](/images/ContainerProcessIsolation.PNG)

The build number (i.e. 14393) only changes when new versions of the OS are published, such as version 1709, 1803, fall creators update, etc. For Windows Server 2016 based hosts/images – the container image’s revision must match the host to be in a supported configuration. Starting with Windows Server version 1709, this is no longer applicable, and the host and container image need not have matching revisions.
Link for details on windows container version compatibility - https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/version-compatibility

## Hyper-V Isolation for Containers
To run container image which has different version from the host, use the Hyper-V isolation. Unlike standard Windows containers, which share the kernel between containers and the host, each Hyper-V isolated container has its own instance of the Windows kernel.

```cmd
docker run -d -it --isolation=hyperv -p 8080:8080 --name <container_name> <image_name>
```

In our case, the same container running nanoserver with build 14393 now **runs** on Windows Server 2019 host having the build 17763.

![Alt text](/images/ContainerHypervIsolation.PNG)

Link for Hyper-V isolation explained - https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/hyperv-container#isolation-explanation

# Query version 
Introduced in version 1709 the cmd prompt and ver command now return the revision details.
```cmd
Microsoft Windows [Version 10.0.16299.125]
(c) 2017 Microsoft Corporation. All rights reserved.

C:\>ver

Microsoft Windows [Version 10.0.16299.125] 
```
The same can also be done for a container after running the below command
eg: docker exec -it <container name/id> <command>

```cmd
docker exec -it mycontainer cmd
```