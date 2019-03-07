# Package an existing .Net Core app using Visual Studio

Take a [self-contained](https://docs.microsoft.com/en-us/dotnet/core/deploying/index) ASP.NET Core service targeting the .NETCoreApp framework. To deploy this .Net Core app as a [Guest Executable](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-existing-app) on Service Fabric, the application has to be packaged with a pre-defined directory structure. This structure is created by Visual Studio if you use the Guest Executable Service Fabric template.
```cmd
MyServiceFabricApp
├───ApplicationManifest.xml             # defines the application like servicetypes, count of instance, partitions
└───ApplicationPackage
    ├───Code                            # contains the service code
    │       appsettings.config
    │       existingapp.exe
    ├───Config                          # contains a Settings.xml to customize configuration settings of service
    │       Settings.xml
    ├───Data                            # optional, stores local data that the service might need
    ├───ServiceManifest.xml             # defines the service like code, configuration, endpoint, etc
```

# Visual Studio to package an existing .Net Core app

1. Choose File > New Project, and create a Service Fabric application. The template can be found under Visual C# -> Cloud. 
2. Choose Guest Executable as the service template.
3. Under the Code Package Folder, browse to the published directory of existing .Net Core service.
4. Under Code Package Behavior you can specify either Add link to external folder. This enable you to update the guest executable in its source as a part of the application package build.
5. Choose the Program that needs to run as service and also specify the arguments and working directory if they are different.
6. For WorkingFolder specify **CodeBase**. WorkingFolder is used to set the correct working directory so that the relative paths can be used by either the application or initialization scripts.
7. If your Service needs an endpoint for communication, add the protocol, port and type to the ServiceManifest.xml
```xml
<Endpoint Protocol="http" Name="MyServiceEndpoint" Type="Input" Port="5000" UriScheme="http" />
```
8. log console output (both stdout and stderr) to one of the service's working directories
```xml
<ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/> 
```

# Modify the existing .Net Core app

In the existing .Net Core app, add [strong wildcard](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore#endpoint-configuration) URL prefix using IWebHostBuilder Interface's UseUrls extension method in Program.cs. This is required to expose the endpoint to the external clients.
```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseUrls("http://+:5000")       //Strong wildcard
        .UseStartup<Startup>();
```

The app is now good to be deployed to the Service Fabric cluster.
