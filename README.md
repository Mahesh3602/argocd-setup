# Prerequisite
working kubenetes cluster

# Objective 2: Set up a Core GitOps Environment with Argo CD
  - create namespace
    kubectl create ns argocd
  - Apply the Argo CD installation manifest
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  - Check resource created in argocd ns (usually 7)
    kubectl get pods -n argocd
  - Expose the Argo CD API server through Argo CD UI in the background
    kubectl port-forward svc/argocd-server -n argocd 8085:443 & 
    - The & at the end of the command runs the command in the background.keep it open.
  - Verify
    wget --no-check-certificate https://localhost:8080

# Install and configure the Argo CD CLI
- Download the CLI.
    curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
    chmod +x argocd
    sudo mv argocd /usr/local/bin

    OR brew install argocd

- Get the initial admin password.
    argocd admin initial-password -n argocd
    JL-z5OoQUdN2TDXN
- Login to Argo CD, with the admin password
    argocd login localhost:8085 --username admin --password JL-z5OoQUdN2TDXN --insecure

# create deployment, service and yaml files and commit to git

# create PAT in git and allow content acces
  export GH_TOKEN=<GH-TOKEN>

# Add the repository to Argo CD
argocd repo add https://github.com/Mahesh3602/argocd-setup.git --username Mahesh3602 --password $GH_TOKEN

# Create an Argo CD application named my-argocd-test
     argocd app create my-argocd-test \
       --repo https://github.com/Mahesh3602/argocd-setup.git \
       --path . \
       --dest-namespace default \
       --dest-server https://kubernetes.default.svc \
       --sync-policy=manual