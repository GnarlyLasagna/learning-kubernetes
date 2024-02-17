# essential notes from learning-kubernetes on Boot.Dev

Install kubectl
```
brew install kubectl
```

install minikube
```
brew install minikube
```

start Minikube: After the installation is complete, you can start Minikube by running:
```
minikube start --extra-config "apiserver.cors-allowed-origins=["http://boot.dev"]"
```

This will take a few minutes to complete the first time. The extra configuration is just so we can hit your cluster from Boot.dev. You should see a message like "kubectl is now configured to use "minikube" cluster and "default" namespace by default".

run this to open a browser window with a locally hosted dashboard for your cluster. You can use this dashboard to view and manage your cluster. We won't be using it much in this course, but it's a great tool to know about
```
minikube dashboard
```

this command will create a "deployment" for us
```
kubectl create deployment
```
```
kubectl create deployment synergychat-web --image=lanecwagner/synergychat-web:latest
```

To make sure the deployment was successful, run:
```
kubectl get deployments
```

In order to access the application from your local network, you'll need to use kubectl to do some port forwarding. First, run:
```
kubectl get pods
```

You should see something like this:
```
NAME                                   READY   STATUS    RESTARTS   AGE
synergychat-web-679cbcc6cd-cq6vx       1/1     Running   0          20m
```
next run 
```
kubectl port-forward PODNAME 8080:8080
```
Be sure to replace PODNAME with your pod's name. In my case, it was synergychat-web-679cbcc6cd-cq6vx

Take a look at the YAML file for your current deployment in the CLI:
```
kubectl get deployment synergychat-web -o yaml
```

Edit the deployment and change the number of replicas from 2 to 10:
```
kubectl edit deployment synergychat-web
```

Make sure you've got 10 pods running:
```
kubectl get pod
```

Keep using kubectl get pod to check on your pods until all 10 are in a "ready" state. Once they are, run:
```
kubectl proxy
```

Let's take a look at the ReplicaSets that are running in your cluster:
```
kubectl get replicasets
```

Let's take a look at the ReplicaSets that are running in your cluster:
```
kubectl get replicasets
```
Just like with pods, notice that we never directly created the replica set. We created a deployment, and the deployment created the replica set.


download a copy of your deployment's YAML file and save it in your current directory:
```
kubectl get deployment synergychat-web -o yaml > web-deployment.yaml
```

Then open it in your text editor. There are 5 top-level fields in the file:

 - apiVersion: apps/v1 - Specifies the version of the Kubernetes API you're using to create the object (e.g., apps/v1 for Deployments).
 - kind: Deployment - Specifies the type of object you're configuring
 - metadata - Metadata about the deployment, like when it was created, its name, and its ID
 - spec - The desired state of the deployment. Most impactful edits, like how many replicas you want, will be made here.
 - status - The current state of the deployment. You won't edit this directly, it's just for you to see what's going on with your deployment.
 - Inside your editor, change the number of replicas to 3 and save the file. Notice that you're just editing a file on your machine! It won't yet have any effect on the deployment in your cluster.

 To apply the changes, run:
```
kubectl apply -f web-deployment.yaml
```
