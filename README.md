# learning-kubernetes

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
