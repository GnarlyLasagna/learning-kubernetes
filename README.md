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

