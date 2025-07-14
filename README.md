# Deploy a Containerized Web Application on DigitalOcean Kubernetes Cluster(DOKS)

The solution involves containerizing web application, building and pushing the docker image, and deploying it on DOKS cluster with horizontal pod autoscalilng(HPA) and load balancing. It will also demonstrate how to add resiliency into the application by automatically scaling to match the demand due to the increasing workload. It will also delve into configuring Horizontal Pod Autoscaling(HPA) to automatically scale the number of pods based on CPU utilization. In order to ensure high availability and scalability of applications it will show how to set up load balancing to distribute the traffic to the kubernetes service.

## Steps to deploy the application

-  Create a Dockerfile

This Dockerfile contains implementation of a sample web application written in Go called hello-app. You can fork the repo or download it.

- Build the Docker image from Dockerfile.

Before deploying it to the cluster, let’s package the dockerfile as a docker image.

```
docker build -t hello-app-doks:latest .
```

- Push the Docker image to Docker hub
```
docker tag hello-app-doks:latest adph33145/hello-app-doks:latest
docker push adph33145/hello-app-doks:latest
```

- Create a DigitalOcean Kubernetes cluster

- Deploy the application 

Let’s create a kubernetes deployment to run the app on the cluster
```
kubectl create deployment hello-app --image=adph33145/hello-app-doks:latest
```
- Scale the deployment

You can scale the deployment to 3 replicas and continue to add based on the application usage. 
```
kubectl scale deployment hello-app --replicas=3
```

- Configure Load Balancing

Pods created by the deployment receive an ephemeral IP. You can create a service that can load-balance client connections across all the pods scheduled. The service of type ClusterIP creates a static IP and DNS hostname that can be consumed by the clients within the cluster whereas a service of type Loadbalancer is used to expose the application to external traffic outside the cluster. 
```
kubectl expose deployment hello-app --name=hello-app-service --type=LoadBalancer --port 80 --target-port 8080
```


- Configure Horizontal Pod Autoscaling(HPA)
  Let’s scale the application through HPA ensuring high availability 
```
kubectl autoscale deployment hello-app --cpu-percent=80 --min=1 --max=5
```



