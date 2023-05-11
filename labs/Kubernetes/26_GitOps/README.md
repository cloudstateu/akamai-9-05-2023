# GitOps Workshop: Learning ArgoCD with Kubernetes

Welcome to this ArgoCD with Kubernetes workshop! Here, we'll be working with ArgoCD, a declarative, GitOps continuous delivery tool for Kubernetes. This workshop is based on the fork from **[cloudstateu/gitops-lab](https://github.com/cloudstateu/gitops-lab)**.

## **Prerequisites**

Before you start the workshop, please ensure you have the following:

- A running Kubernetes cluster (Local clusters like minikube or Docker Desktop work too)
- kubectl installed and configured to interact with your cluster
- A GitHub account
- Basic understanding of Kubernetes and GitOps principles

## **Installation**

### **Install ArgoCD on your Kubernetes cluster**

We'll start by installing ArgoCD on your Kubernetes cluster. Run the following commands:

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### **Access the ArgoCD User Interface**

ArgoCD provides a user interface where you can visualize and control your deployments. You can access it by running these commands:

```
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd
```

### **Login to ArgoCD**

By default, ArgoCD creates an **`admin`** user. You can retrieve the auto-generated password using this command:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" base64 --decode && echo
```

Login using the **`admin`** user and the token outputted by the above command.

Once you're logged in, it's a good practice to change and delete the initial password. Refer to ArgoCD's documentation for the procedure.

### ****Update `application.yaml` with Forked Repository URL**

Before deploying an application to Azure Kubernetes Service (AKS), we need to make sure that the ArgoCD application's configuration points to the correct Git repository. The repository should be the one where your application's Kubernetes manifests are stored.

For this task, you'll update the **`application.yaml`** file with the URL of your forked repository.

1. Open the **`application.yaml`** file in a text editor of your choice.
2. Locate the **`source`** section:
    
    ```
    yamlCopy code
    source:
        repoURL: <place-url-to-forked-repo>
        targetRevision: HEAD
        path: dev
    
    ```
    
3. Replace **`<place-url-to-forked-repo>`** with the URL of your forked repository:
    
    ```
    yamlCopy code
    source:
        repoURL: https://github.com/your-username/your-forked-repo
        targetRevision: HEAD
        path: dev
    
    ```
    
4. Save and close the **`application.yaml`** file.

### **Deploy an Application to Azure Kubernetes Service (AKS)**

You can use ArgoCD to manage applications on your AKS cluster. Here's an example of how to do this using an **`application.yaml`** file:

```
kubectl apply -f application.yaml
```

This command will create an ArgoCD application based on the specifications defined in your **`application.yaml`** file.

### **Exercise 1: Changing Replica Count in Kubernetes Deployment**

In this exercise, you will change the number of replicas for your deployment directly from the Kubernetes cluster.

1. Check your current deployment's replica count:
    
    ```
    kubectl get deployment <your-deployment-name> -n <your-namespace>
    ```
    
2. Update the replica count:
    
    ```
    
    kubectl scale deployment <your-deployment-name> --replicas=<new-replica-count> -n <your-namespace>
    ```
    
3. Verify the change in ArgoCD UI.

### **Exercise 2: Changing Replica Count in Git**

In this exercise, you will change the number of replicas for your deployment directly from Git.

1. Open your **`deployment.yaml`** file in your Git repository.
2. Locate the **`replicas`** field and change its value to the desired replica count.
3. Commit and push your changes.
4. Wait for ArgoCD to sync the changes. Verify the change in the ArgoCD UI.

### **Exercise 3: Manual Sync in ArgoCD**

In this exercise, you will manually sync ArgoCD to apply changes that you have made on the Git side.

1. Make a change in your Git repository (for example, modify the **`replicas`** field in your **`deployment.yaml`** file).
2. Commit and push your changes.
3. Go to the ArgoCD UI, select your application, and click on the "Sync" button.
4. Verify the change in the ArgoCD UI.

### **Exercise 4: Understanding autoSync**

The **`autoSync`** attribute, when set to true, allows ArgoCD to automatically apply changes from the Git repository to the Kubernetes cluster.

1. Set **`autoSync: true`** in your **`application.yaml`** file.
2. Make a change in your Git repository (for example, modify the **`replicas`** field in your **`deployment.yaml`** file).
3. Commit and push your changes.
4. Observe that ArgoCD automatically synchronizes the changes.
5. Set **`autoSync: false`** in your **`application.yaml`** file.
6. Again, make a change in your Git repository.
7. This time, ArgoCD doesn't synchronize the changes automatically. You must manually sync from the ArgoCD UI.

### **Exercise 5: Understanding selfHeal**

The **`selfHeal`** attribute allows ArgoCD to automatically revert changes made directly in the Kubernetes cluster that do not match the Git repository.

1. Set **`selfHeal: true`** in your **`application.yaml`** file.
2. Change the number of replicas for your deployment directly in the Kubernetes cluster.
3. Observe that ArgoCD automatically reverts the change to match the Git repository.
4. Set **`selfHeal: false`** in your **`application.yaml`** file.
5. Again, change the number of replicas for your deployment directly in the Kubernetes cluster.
6. This time, ArgoCD doesn't revert the change automatically. The change remains until the next sync.

### **Exercise 6: Understanding prune**

The **`prune`** attribute allows ArgoCD to automatically delete Kubernetes resources that are no longer present in the Git repository.

1. Set **`prune: true`** in your **`application.yaml`** file.
2. Remove a Kubernetes resource from your Git repository.
3. Commit and push your changes.
4. Observe that ArgoCD automatically deletes the corresponding resource in the Kubernetes cluster.
5. Set **`prune: false`** in your **`application.yaml`** file.
6. Again, remove a Kubernetes resource from your Git repository.
7. This time, ArgoCD doesn't delete the corresponding resource in the Kubernetes cluster automatically. The resource remains until the next sync when **`prune: true`**.