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

You should get a warning that lets you know that you're missing the last-applied-configuration annotation. That's okay! we got that warning because we created this deployment the quick and dirty way, by using kubectl create deployment instead of creating a YAML file and using kubectl apply -f

However, because we've now updated it with kubectl apply, the annotation is now there, and we won't get the warning again.

Download the YAML file again and take a look at it. You should see the annotation now.

Apply the configuration a second time, you won't get the warning. Save this YAML file in a git repo for this course! We'll be making more configuration files. Kubernetes is an "infra-as-code" tool, so it's important to keep your configuration files in a git repo.

Finally, start the proxy server:
```
kubectl proxy
```

Let's write a deployment from scratch.

Feel free to reference the k8s docs here as you go for examples of the proper structure.

Create a new file called api-deployment.yaml.
Add the apiVersion and kind fields. The apiVersion is apps/v1 and, since this is a deployment, the kind is Deployment.
Add a metadata/name field, and let's name our deployment synergychat-api for consistency.
Add a metadata/labels/app field, and also set it to synergychat-api. This will be used to select the pods that this deployment manages.
Add a spec/replicas field and let's set it to 1. We can always scale up to more pods later.
Add a spec/selector/matchLabels/app field and set it to synergychat-api. This should match the label we set in step 4.
Add a spec/template/metadata/labels/app field and set it to synergychat-api. Again, this should match the label we set in step 4. Labels are important because they're how Kubernetes knows which pods belong to which deployments.
Add a spec/template/spec/containers field. This actually contains a list of containers that will be deployed:
Note: A hyphen is how you denote a list item in yaml
Set the name of the container to synergychat-api.
Set the image to lanecwagner/synergychat-api:latest. This tells k8s where to download the Docker image from.

CREATE THE DEPLOYMENT
```
kubectl apply -f api-deployment.yaml
```

Next, take a look at all the pods you have running now. You should see pods for the web service and a pod for the api service.

However, you might notice that the api pod isn't in a "ready" state. In fact, it should be stuck in a "CrashLoopBackOff" status. Oh no! We've created a thrashing pod!

Run:
```
kubectl proxy
```

## CONFIG MAPS
There are several ways to manage environment variables in Kubernetes. One of the most common ways is to use ConfigMaps. ConfigMaps allow us to decouple our configurations from our container images, which is important because we don't want to have to rebuild our images every time we want to change a configuration value.

In a Dockerfile we can set environment variables like this:

```
ENV PORT=3000
```
Copy icon
The trouble is, that means that everyone using that image will have to use port 3000. It also means that if we want to change the port, we have to rebuild the image.

ASSIGNMENT
First, let's take a closer look at our crashing pod and try to figure out why it's crashing.

kubectl get pods
Copy icon
Copy the pod name and then get the logs:

```
kubectl logs <pod-name>
```
Copy icon
You should see that a specific environment variable is missing! Let's fix that.

Create a new file. Let's call it api-configmap.yaml. Add the following YAML to it:

apiVersion: v1
kind: ConfigMap
metadata/name: synergychat-api-configmap
data/API_PORT: "8080"
Next, apply the config map:

```
kubectl apply -f api-configmap.yaml
```
Now, we haven't yet connected the config map to our pod, so it should still be crashing. However, for now, let's just validate that the config map was created successfully:
```
kubectl get configmaps
```
Run:
```
kubectl proxy
```
