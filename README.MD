# Project with EKS and ArgoCD
Create and delete the cluster with eksctl
```yaml
eksctl create cluster --name argocd-cluster --version 1.29 --region us-east-1 --nodegroup-name ng-1 --node-type t3.medium --nodes 2 --nodes-min 1 --nodes-max 3
eksctl delete cluster --name argocd-cluster --region us-east-1
```
Install ArgoCD inside the cluster

	- Create a seperate name space for ArgoCD
```yaml
		kubectl create namespace argocd
```
	- Install argocd
```yaml
	    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
	    kubectl get pods -n argocd
```

NB. by default Argocd is deployed in ClusterIP we need to change it in ALB to be able to access it aoutside the network

Look for the server name: kubectl get svc -n argocd

    - Edit it: kubectl edit svc argocd-server -n argocd - change clusterIP to LoadBalancer

    - Run kubectl get svc -n argocd - Copy the ALB name and access it through your browser

    - Get the password: kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Build the docker image: docker build -t efritznel/pyapp:v1 .

Push to Docker: docker push efritznel/pyapp:v1

Deploy the application with kubectl

	- Change directory: cd k8s

	- kubectl apply -f ./

# Troubleshoot
```yaml
	- kubectl get pods
	- kubectl describe pod podname
	- kubectl top pod podname
	- kubectl logs podname
	- kubectl get events
```

Test the app is running: kubectl get svc

Grab the ALB name and access it through your browser

# Setup ArgoCD dashboard

	- Login to ArgoCD with the Username and password

	- Go to "New App" 

	- Give it a name "gha-demo" - Project Name "default" -  Sync Policy "Automatic"

	- Under "Source" provide your Repository URL "https://github.com/efritznel/my-project"

	- Revision "main"

	- Path "the path of your manifest files" Ex: k8s

	- Cluster URL "Select the cluster where ArgoCD is running"

	- Namespace "default"

# What is YQ?

In DevOps terms, yq is a command-line YAML processor — think of it as:

👉 jq for YAML

It lets you read, modify, and transform YAML files programmatically, which is why it’s heavily used in:

	- Kubernetes manifests

	- Helm values files

	- GitOps pipelines (Argo CD, GitHub Actions, Jenkins)

	- CI/CD automation

yq = Infrastructure automation scalpel for YAML

If you’re doing Kubernetes, Helm, GitOps, or GitHub Actions (like your ArgoCD pipeline), yq is basically a must-have tool.
