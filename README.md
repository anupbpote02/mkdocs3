## Overview
This repository provides a detailed guide on setting up **Spinnaker**, an open-source continuous delivery platform, on a **Minikube (Kubernetes)** cluster using **Halyard**. It includes configuration for MinIO storage, Docker Registry, and Kubernetes deployment.

## Contents
- Install Minikube, Kubectl, and Halyard
- Configure MinIO for S3 storage
- Add Docker registry
- Enable Kubernetes and deploy Spinnaker
- Access the Spinnaker UI locally



## Deploying Spinnaker on Minikube (Kubernetes) using Hal

<p>&nbsp;</p>


- **Spinnaker** is an open source, multi-cloud continuous delivery platform for releasing software changes with high velocity and confidence. 
- **Kubernetes (k8s)** is an open-source container orchestration system for automating     computer application deployment, scaling, and management.

- **Minikube** provides a way to install and run Kubernetes locally.     Minikube runs a single-node Kubernetes cluster inside a VM on your laptop     for users looking to try out Kubernetes or develop with it day-to-day.


- **Kubect**l  is the Kubernetes     command-line tool that allows you to run commands against Kubernetes     clusters. You can use kubectl to deploy applications, inspect and manage     cluster resources, and view logs.


- **Halyard**     is a CLI tool for configuring, installing, and updating Spinnaker.



**Step 1:**  Installing minikube 

 To install Minikube, run the following commands for linux OS x86-64 architecture.

```bash
·    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 

·    sudo install minikube-linux-amd64 /usr/local/bin/minikube.
```

<center>[*for other OS , check this link*:](https://minikube.sigs.k8s.io/docs/start/) </center>

---



**Step 2 :** stalling kubectl   Install using native package management

```bash
·   sudo apt-get update

·   sudo apt-get install -y apt-transport-https ca-certificates curl

·   sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

·   echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

·   sudo apt-get update

·   sudo apt-get install -y kubectl
```

<center>*[for other OS , check this link]( https://kubernetes.io/docs/tasks/tools/)*</center>

---



**Step 3**: Install halyard

<center> [*check this link to download wrt to your OS*](https://minikube.sigs.k8s.io/docs/start/)</center> 

---



**Step 4:** Install minio for K8s

<center> [*check this link to download wrt to your OS*](https://min.io/download#/kubernetes) : </center>

---



**Step 5 :** Start minikube cluster

```bash
·   minikube start --cpus 4 --memory 8192
```

---



**Step 6 :** To start minio

```
·   kubectl minio init      
```



---



**Step 7**: configure for minikube deployment

At this point you should have the Minio server up and running alongside Minikube.

```bash
·    kubectl get nodes
```

---




**Step 8:** setting up minio for S3 storage 

```bash
·    kubectl minio tenant create tenant1 --servers 4 --volumes 16 --capacity 16Ti
```

----




**Step 9**: setup spinnaker storage



```bash
·   hal config storage s3 edit --access-key-id minio --secret-access-key --region us-east-1 --endpoint http://127.0.0.1:9000


·   hal config storage edit --type s3
```



----




**Step 10** : configure docker registry 

 

At least a docker registry is required for the Spinnaker kubernetes setup. You can add the public docker registry:

```bash
·   hal config provider docker-registry account add dockerhub --address index.docker.io --repositories library/nginx
```



​                                  <center>**(or)**</center>

 you can even add your private Docker registries:

```bash
·   hal config provider docker-registry account add docker-private-repo --address https://my-private-docker-repo.example.com --username myuser --password
```


 **<u>*Note :*</u>**

** *if the following error occurs ERROR Account "docker-private-repo" appears more than once? Change the name of the account in dockerRegistry***.***Change the name of your docker registry.***

-----



**Step 11:** You then need to enable the docker-registry provider:



```
   hal config provider docker-registry enable
```



----



 **Step 12**: Configure the Spinnaker K8’s provider:

```
·  hal config provider kubernetes account add my-k8s-account --docker-registries dockerhub --context $(kubectl config current-context)

·  hal config provider kubernetes enable

·  hal config deploy edit --type=distributed --account-name my-k8s-account
```



check and change your kubernetes account name , if it gives some error.

----



**Step 13 :** Configure Spinnaker version

 To view the full version list:

```bash
·   hal version list
```



To setup the desired version:

```bash
·   hal config version edit --version 1.8.0
```

---



 **Step 14** **:** Deploy Spinnaker to minikube



```
·   hal deploy apply                
```



 This will take a while. Hal will try to spin up all the required Spinnaker pods in the Kubernetes cluster.
 You can see them in action by running , You can run the below command on other terminal

```
·  kubectl get pods --namespace spinnaker
```



----



 **Step 15 :** Connect to Spinnaker



Once everything is done, it’s time to connect to the Spinnaker UI. You can run 

```
·  hal deploy connect                       
```

Open your browser and go to http://localhost:9000/

 to access the deck UI.



![spinak](C:\Users\AP086540\pro\mypro\docs\img\spinak.png)





![spinak3](https://spinnaker.io/docs/guides/user/pipeline/managing-pipelines/images/pipelines-tab.png)

 

 
