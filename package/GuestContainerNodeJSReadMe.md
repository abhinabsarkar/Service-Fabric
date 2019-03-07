# Run a NodeJS app as guest container (Windows OS) on Service Fabric

Running an existing application in a Windows container on a Service Fabric cluster doesn't require any changes to the application. To run a container on Service Fabric cluster, the host OS and the container OS must be the same. One cannot run Windows containers on a Linux Service Fabric cluster

## Pre-requisites

The following are required to run a windows container on Service Fabric cluster
* Development computer running
    - Visual Studio 2017 with [Service Fabric SDK](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)
    - Docker for Windows. [Get Docker CE for Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). After installing and starting Docker, **right-click on the tray icon and select Switch to Windows containers**. This step is required to run Docker images based on Windows.
* Service Fabric cluster (version 6.4) with 3 or 5 nodes running Windows Server 2019 with Containers feature installed. If using different version, refer the [Windows - Container version compatibility](/containers/WindowsContainersCompatibilityReadMe.md)
* Container registry. If you don't have a container registry available, for development purpose, you can use a local one. Follow steps to run [local windows registry](/package/WindowsLocalRegistryReadMe.md)

## Define the Docker file

Create a file Dockerfile (with no file extension). Add the following to Dockerfile and save your changes:
```cmd
#Use an official nanoserver as the base image
FROM microsoft/nanoserver

# Create app directory
WORKDIR /usr/src/app

# Copy the contents of the application folder
COPY . .

# Expose the port
EXPOSE 8080

# Run the node app
CMD [ "npm", "start" ]
```

## Build the image
1. Build your NodeJS application locally. 
2. Place node.exe in the NodeJS application folder. This is required if you are not installing node on the container.
3. Place the above Dockerfile in the NodeJS application folder.
4. Build the image by running the below command

```cmd
docker build -t <imagename> .
Example
docker build -t mynanonodeimage .
```

## Test the image locally
Run the image locally to test (-d detached mode; -it interactive shell; -p publish on port hostport:containerport; --name name of the container; imagename) [Docker run reference](https://docs.docker.com/engine/reference/run/)

```cmd
docker run -d -it -p <hostport>:<containerport> --name <containername> <imagename>
Example
docker run -d -it -p 8080:8080 --name mynanonodecontainer mynanonodeimage
```
If the image OS is not compatible as a container process with the host OS, run it with hyperv isolation.

```cmd
docker run -d --isolation=hyperv -it -p 8080:8080 --name mynanonodecontainer mynanonodeimage
```

Once the container starts, find its IP address so that you can connect to your running container from a browser
```cmd
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" mynanonodecontainer
```

Connect to the container **locally** by opening a web browser pointing to the IP address returned, for example "http://172.30.10.61:8080".

To connect from a different machine, use the ip of the **host machine** followed by the port exposed.

## Push the image to container registry
To push and pull images to/from a container registry, follow the steps documented here - [local windows registry](/package/WindowsLocalRegistryReadMe.md)

## Create the containerized service in Visual Studio
The Service Fabric SDK and tools provide a service template to help you create a containerized application.

1. Start Visual Studio. Select File > New > Project.
2. Select Service Fabric application, name it "MyFirstContainer", and click OK.
3. Select Container from the list of service templates.
4. In Image Name enter "containerregistry/imagename", the image you pushed to your container repository. In case you are using local registry mentioned above, it will look like "10.45.20.3:5000/myimagelocal".
5. Give your service a name, and click OK.

## Update the below configuration
Application Manifest configuration
```xml

     <Parameters>
       <!-- set the instance to 1 if you are running a single 1 VM multi node dev cluster-->
       <Parameter Name="MyNanoNodeApp_InstanceCount" DefaultValue="1" />
     </Parameters>
      ...

      <!-- container isolation mode -->  
      <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">        
        <RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
        <!-- container port-to-host port mapping -->
        <PortBinding ContainerPort="8080" EndpointRef="MyNanoNodeAppTypeEndpoint" />
      </ContainerHostPolicies>
```

Service Manifest configuration
```xml
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <!-- container registry and the image name -->
        <ImageName>10.45.20.3:5000/myimagelocal</ImageName>        
      </ContainerHost>
    </EntryPoint>

    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyNanoNodeAppTypeEndpoint" Port="8080" UriScheme="http" Protocol="http" />
    </Endpoints>    
```
## Publish the application to Service Fabric cluster
Follow either of the below steps to publish app
* [Deploy an application package using Powershell](/deployment/PSDeployReadMe.md)
* [Deploy an application package using Visual Studio IDE](/deployment/VSDeployReadMe.md)

## Common issue(s)
If you encounter an error with the below message, even though you have installed Docker for Windows along with Container features enabled on the VM:
```error
'There was an error during download. Container deployment is not supported on the node.'
```
It means that Service Fabric is unable to find the docker daemon i.e. dockerd.exe. Set the PATH in the environment variables for dockerd & restart the VM.
