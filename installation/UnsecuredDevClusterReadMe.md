# Install Service Fabric Dev Cluster

To create the cluster, download [Service Fabric Standalone Package - Windows Server ](https://go.microsoft.com/fwlink/?LinkId=730690)

Unpack the standalone package on the target machine. Open the config file "ClusterConfig.Unsecure.DevCluster.json" and replace "localhost" below nodeName with the IP address of the target machine. Since it's a single VM with 3 nodes, use the same IP address for all 3 nodes. Then run the CreateServiceFabricCluster.ps1 script through an administrator PowerShell session, from the standalone package folder.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
**This step is only required if the target machine is unable to connect to internet.**
If the machine is not connected to internet, download the [Service Fabric runtime package](https://go.microsoft.com/fwlink/?linkid=839354) in a separate machine. Create a folder "DeploymentRuntimePackages" in the standalone package folder and place the runtime inside the "DeploymentRuntimePackages" folder. Run the below command where x.x.xxx.xxxx will be the version of the runtime package. 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -FabricRuntimePackagePath .\DeploymentRuntimePackages\MicrosoftAzureServiceFabric.x.x.xxx.xxxx.cab -AcceptEULA
```

# Validate cluster creation

* Browser - Launch the Service Fabric Explorer - http://<IP_address>:19080
* PowerShell - Run the below command
```powershell
Connect-ServiceFabricCluster <IPaddress>:19000
```

# Remove Service Fabric from the current machine
```powershell
.\CleanFabric.ps1
```

If the cluster isn't cleared completely, run the below commands in **admin mode**. Clear the Service Fabric rgistry key and all its values.

```cmd
C:\Windows\system32>sc.exe stop KtlLogger
SERVICE_NAME: KtlLogger
        TYPE               : 1  KERNEL_DRIVER
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
C:\Windows\system32>sc.exe stop LeasLayr
SERVICE_NAME: LeasLayr
        TYPE               : 1  KERNEL_DRIVER
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```