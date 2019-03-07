# Deploy packaged application on Service Fabric cluster

Steps to deploy a packaged application on Service Fabric cluster

1. Connect to the Service Fabric cluster from a client machine.
2. Copy the application package (compress if the package is large) to the image store from the client machine
3. Register the application on the Service Fabric cluster
4. Create the application on the cluster
5. Remove the application package from the image store. Keeping unused application packages consumes disk storage and leads to application performance issues.

```powershell
PS C:\WINDOWS\system32> Connect-ServiceFabricCluster 10.245.54.35:19000
True

ConnectionEndpoint   : {10.245.54.35:19000}
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-733fe4ce-b6d6-4dee-869a-04ce235501e3
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       ConnectionIdleTimeout                : 00:00:00
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:30
                       NotificationGatewayConnectionTimeout : 00:00:30
                       NotificationCacheUpdateTimeout       : 00:00:30
                       AuthTokenBufferSize                  : 4096
                       }
GatewayInformation   : {
                       NodeAddress                          : 10.245.54.35:19000
                       NodeId                               : 85772935593a0315f92e3293832c5fe9
                       NodeInstanceId                       : 131934684788076766
                       NodeName                             : vm0
                       }

PS C:\WINDOWS\system32> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'D:\POC\VisualObjectsPkg' -ImageStoreConnectionString 'fabric:ImageStore' -CompressPackage -ApplicationPackagePathInImageStore 'VisualObjectsApplicationType'
Upload to Image Store succeeded

PS C:\WINDOWS\system32> Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'VisualObjectsApplicationType'
Register application type succeeded

PS C:\WINDOWS\system32> New-ServiceFabricApplication -ApplicationName 'fabric:/VisualObjects' -ApplicationTypeName 'VisualObjectsApplicationType' -ApplicationTypeVersion 1.0.0

ApplicationName        : fabric:/VisualObjects
ApplicationTypeName    : VisualObjectsApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}

PS C:\WINDOWS\system32> Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore 'VisualObjectsApplicationType'
Using ImageStoreConnectionString='fabric:ImageStore'
Remove application package succeeded
```