# Containerized docker registry for Windows

Run your own Docker Registry on your laptop or a server, in a Windows Docker container. Since windows still have a problem contacting containers from your Docker host locally, you can pull the image from another server only(The image can be pushed locally). 

**Ensure Docker for Windows is swithced to Windows containers.***

setup a local folder to persist your images
```cmd
mkdir C:\registry
```

If you don't have SSL certificates for your domain and don't wont to use self-signed certificates you can just setup you registry as follows
```cmd
docker run -d -p 5000:5000 --restart=always --name myregistry -v C:\registry:C:\registry stefanscherer/registry-windows:2.6.2
```

## Update the Docker engine config file for image Push & Pull

Edit the docker engine config file "C:\ProgramData\docker\config\daemon.json", to add the host as insecure registry. This is required so that the image can be pushed to the server locally.
```json
{
  "insecure-registries": ["<ip/hostname>:5000"]
}
```
Restart the docker engine

Repeat the step to update the config file for the server/computer which will be pulling the image from this registry.

## Push image to the registry

1. Tag your image correctly first with your registryhost
2. Docker push using that same tag

**Docker Tag**
```cmd
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
docker tag <image_id> <ip/hostname:port>/<new_image_name>
Example
docker tag 518a41981a6a 10.45.20.3:5000/myimagelocal
```

**Docker Push**
```cmd
docker push NAME[:TAG]
docker push <ip/hostname:port>/<new_image_name>
Example
docker push 10.45.20.3:5000/myimagelocal
```