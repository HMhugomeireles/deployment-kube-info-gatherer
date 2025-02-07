# Kubernetes info-gatherer pod

Simple application meant to debug on kubernetes cluster environment
Displays a table with many metadata info that helps you to understand the environment that you are at.

## Getting Started

Node base application using OS module to get:

- freemem
- homedir
- hostname
- platform
- release
- totalmem
- etc

And Kubernetes AWS pod API based on
Metadata Service API:![Status](https://img.shields.io/badge/Status-not%20complete-red)


 Amazon Web Services (AWS)  
 `http://169.254.169.254/latest/meta-data/ami-id`                            
 https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html            

 Google Cloud               
 `http://metadata.google.internal/computeMetadata/v1/instance/machine-type`  
  https://cloud.google.com/compute/docs/storing-retrieving-metadata                         

 Microsoft Azure            
 `http://169.254.169.254/metadata/instance?api-version=2017-12-01`           
  https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service 
 
 DigitalOcean               
 `http://169.254.169.254/metadata/v1/`                                       
  https://www.digitalocean.com/docs/droplets/resources/metadata/                            

 OpenStack                  
 `http://169.254.169.254/openstack/latest`
 https://blogs.vmware.com/openstack/introducing-the-metadata-service/                      
 
 Rancher (Kubernetes)       
 `http://rancher-metadata/2015-07-25/`
https://rancher.com/introducing-rancher-metadata-service-for-docker/                      




http://169.254.169.254/latest/meta-data to get:

- ipv4
- ami id
- local host name
- public host name
- etc


**Note**: This application was made to Kubernetes on AWS, 
Even it works on a basic Docker environment, it will not be able to show all the parameters.


### Using the Application

This app is stored on [DOCKER HUB](https://cloud.docker.com/repository/docker/b4lddocker/deployment-kube-info-gatherer) repository


#### Using it With Docker

In order to use it with docker, the container must know the docker socket, so it is mandatory that you map it with the volume.

```bash
docker run -d --name infog -p 4499:4499 -v /var/run/docker.sock:/var/run/docker.sock b4lddocker/deployment-kube-info-gatherer:latest
```

---

NOTE: If you are on Windows Docker-for-Desktop - you may whant to map like this instead.

```bash
docker run -d --name infog -p 4499:4499 -v //var/run/docker.sock:/var/run/docker.sock b4lddocker/deployment-kube-info-gatherer:latest

```


#### Using it With Kubernetes

You can pull it and use it on you deployment.yaml.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-infog
  # namespace: infog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kube-infog
  template:
    metadata:
      labels:
        app: kube-infog
    spec:
      containers:
      - name: kube-infog
        image: b4lddocker/deployment-kube-info-gatherer:latest
        imagePullPolicy: Always
        ports:
          - containerPort: 4499 #Container/Application
            name: http
        volumeMounts:
        - name: dockersock
          mountPath: /var/run/docker.sock
      volumes:
      - name: dockersock
        hostPath:
          path: /var/run/docker.sock
---
apiVersion: v1
kind: Service
metadata:
  name: kube-infog-service
  # namespace: infog
  labels:
    app: kube-infog
spec:
  selector:
    app:  kube-infog
  type: NodePort
  ports:
  - name:  http
    port:    4499 #Same the Ingress/Loadbalancer
    targetPort: 4499 #Bind to container/Application
    
```




Or you can clone this repo and make it your own to build it.
```
docker build -f Dockerfile -t [ImageName]:[ImageVersion] .
```


## Built With

* [Node.js](https://nodejs.org/en/) - Base Application Framework

## Contributing

Any change and pull request are welcome.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/b4ld/deployment-kube-info-gatherer/tags). 

## Authors

* **b4ld** - *Initial work* - [b4ld](https://github.com/b4ld)


## License

This project is licensed under the MIT License

## Acknowledgments

* Hat
* Inspiration
* l
