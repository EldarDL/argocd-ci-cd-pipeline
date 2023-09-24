![Build Status](https://github.com/EldarDL/argocd-ci-cd-pipeline/actions/workflows/docker-image.yml/badge.svg)

# Setting Up an Application with ArgoCD and Minikube

## Prerequisites

- Docker
- Minikube
- kubectl
- ArgoCD

## Setup Instructions

1. Created a GitHub Repository
   - Create a new GitHub repository to host the source code and deployment files for your application.

2. Created a GitHub Token
   - Create a new GitHub token here: https://github.com/settings/tokens

3. Cloned GitHub Repository
   - Clone the GitHub repository to your local machine using the following commands:
     ```
     git clone <github-repo-url>
     cd <repo-directory>
     ```

4. Added Files to GitHub Repository
   - Added the necessary files to GitHub repository. This includes the contents of a zip file containing important application files. To make these changes available remotely, pushed the files using the following commands:
     ```
     git add .
     git push
     ```

5. Installed Docker

6. Added Authentication to Docker Configuration
   - Added authentication for GitHub Container Registry (GHCR) to Docker configuration:
     ```
      echo -n "<github-account>:<github-token>" | base64
     ```
     added this string to `~/.docker/config.json`:
     ```
      {
        "auths": {
                "ghcr.io":
                {"auth":"<base-64-string>"}
        }
      }
     ```
7. Created Docker Authentication Secret for deployment
   - Created a Kubernetes secret named `ghcr-docker-secret` to securely allow deployment to authenticate with GHCR:
     ```
     kubectl create secret generic ghcr-docker-secret \
     --from-file=.dockerconfigjson=$HOME/.docker/config.json \
     --type=kubernetes.io/dockerconfigjson
     ```

8. Installed Minikube
   - Installed Minikube on local machine by following the instructions provided in the official Minikube documentation.
     After installation started minikube server:
     ```
      minikube start --driver=docker
     ```

9. Installed ArgoCD on Minikube cluster
   - commands:
      ```
      kubectl create ns argocd
      kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.5.8/manifests/install.yaml
      ```
10. Port Forward ArgoCD Server
    - To access the ArgoCD web interface, port forward the ArgoCD server to your local machine using the following command:
      ```
      kubectl port-forward svc/argocd-server -n argocd 8080:443
      ```

11. Changed GitHub Repo to Private
    - Changed the visibility of GitHub repository to private

12. Connected to Repo Using SSH
    - Configured SSH access to private GitHub repository in ArgoCD

13. Added Argo Application
    - command:
      ```
      kubectl create -f argo-app/app.yaml
      ```

14. Enabled Minikube Addons For Ingress
    - Enabled the Minikube addons for Ingress and Ingress DNS to handle external traffic to your applications:
      ```
      minikube addons enable ingress
      minikube addons enable ingress-dns
      ```

15. Run Minikube Tunnel
    - Run Minikube tunnel to expose services to local machine:
      ```
      minikube tunnel
      ```

16. Updated Hosts File
    - Added an entry to local `hosts` file to map a hostname (e.g., `127.0.0.1 argo-app`) to Minikube cluster's IP address:
      ```
      127.0.0.1     argo-app.com
      ```
      
