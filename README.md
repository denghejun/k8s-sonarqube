## installing sonarqube in kubernetes
https://coderise.io/installing-sonarqube-in-kubernetes/

sonar quality code
SonarQube is the open source platform to analyze code and continuously inspect code quality of applications. SonarQube also displays health of applications as well as discover any code vulnerabilities. In our previous blog, we discussed the importance of Continuous Integration while working with Kubernetes. In this blog, we will install SonarQube in Kubernetes and analyze a sample application using SonarQube.

sonarqube in kubernetes setup
pre-requisites:
Linux or Windows machine
Kubernetes Cluster in running state
Kubectl client installed
You can install kubectl from this link. If you don’t have a running Kubernetes cluster, you can refer this blog which covers setting up a Kubernetes cluster on Azure or you can also create one manually using minikube.

step 1 | clone the github repository
We have published all Kubernetes manifests for SonarQube in a repo. You can view the code repository on Github here.

$ git clone https://github.com/coderise-lab/k8s-sonarqube
step 2 | make sure your kubernetes cluster is up and running
$ kubectl get nodes
NAME                     STATUS    ROLES     AGE       VERSION
jakir-hp-probook-4530s   Ready     <none>    18h       v1.8.0
step 3 | create secret for postgres database
This SonarQube installation works with Postgres. But you can change it to other database like MySQL. Postgres is limited for this example only. To secure passwords, we do create secrets in Kubernetes. For postgres, the following secret is created.

$ kubectl create secret generic postgres-pwd --from-literal=password=CodeRise_Pass
step 4 | run manifests
$ kubectl create -f sonar-postgres-secret.yaml     
$ kubectl create -f sonar-pv-postgres.yaml     
$ kubectl create -f sonar-pvc-postgres.yaml  
$ kubectl create -f sonar-postgres-deployment.yaml  
$ kubectl create -f sonarqube-deployment.yaml
$ kubectl create -f sonarqube-service.yaml
$ kubectl create -f sonar-postgres-service.yaml 
This will create pods in the cluster. You can check the pods with,

$ kubectl get po -o wide 
NAME                              READY     STATUS    RESTARTS   AGE       IP           NODE
sonar-postgres-6bb954bcf9-zs6wn   1/1       Running   1          17h       172.17.0.3   jakir-hp-probook-4530s
sonarqube-cb5ccb9-ktfzx           1/1       Running   1          17h       172.17.0.2   jakir-hp-probook-4530s
When these two pods are in running state, then it means your SonarQube installation is successful. You can access SonarQube Web UI using Kubernetes services.

Check Sonar service:

$ kubectl get svc
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP        18h
sonar            NodePort    10.102.242.193   <none>        80:31815/TCP   17h
sonar-postgres   ClusterIP   10.102.27.144    <none>        5432/TCP       17h
This cluster is running on localhost. For Sonar service, 31815 port is exposed. So you are able to access the service on http://node-ip:31815/sonar. In our example as the cluster running locally it is http://127.0.0.1:31815/sonar.

SonarQube Image

The default username and password for the SonarQube is admin.After login to the sonarqube follow the steps by choosing the language of the project like Java or other. Create the project specific key and hash of the project.
Finally you will get this:

sonar-scanner \
-Dsonar.projectKey=coderisekey \
-Dsonar.sources=. \
-Dsonar.host.url=http://127.0.0.1:31815/sonar \
-Dsonar.login=4bf1806550240eb90246837d008a6a09ec153ba6
Above mentioned is the command to scan the project. For this sonarqube-scanner must be installed on machine on which project will be analyzed.

To download and install sonarqube view this instruction from offical site of the sonarqube.

The sample project repo is inside the repository that you have cloned. The name of the repository is flask.

$ cd flask
$ sonar-scanner \
-Dsonar.projectKey=coderisekey \
-Dsonar.sources=. \
-Dsonar.host.url=http://127.0.0.1:31815/sonar \
-Dsonar.login=4bf1806550240eb90246837d008a6a09ec153ba6
The final output you can see as:

SonarQube Image