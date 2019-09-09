# Chicago ML - Presentation on Kubeflow - Sept 11, 2019

## Getting Started

Nothing straight forward about installing it. Nervous that the only
options involve VMs.

Vagrant / VirtualBox wasn't having it on my system.

Installing k8s using snap under Multipass was trivial.

Installed and configured Kubeflow using [Kubeflow Deployment with
kfctl_k8s_istio
](https://www.kubeflow.org/docs/started/k8s/kfctl-k8s-istio/).

Update: A more complete tutorial is here:

https://tutorials.ubuntu.com/tutorial/get-started-kubeflow

## Steps

### Install Snap

    apt install snapd

### Use Snap to install Multipass

    sudo snap install multipass --beta --classic

### Create a kubeflow VM

    multipass launch --name kubeflow --cpus 4 --mem 12GB --disk 20GB

### Start a shell in the kubeflow VM

    multipass shell kubeflow

### Install microk8s

Note: Install an older version because we want Docker as the CRI. See
https://github.com/ubuntu/microk8s/issues/382.

    sudo snap install microk8s --classic --channel=1.13/stable
    microk8s.status --wait-ready
    sudo microk8s.enable dns
    sudo microk8s.enable storage
    sudo microk8s.enable dashboard
    sudo snap alias microk8s.kubectl kubectl
    kubectl config view --raw > ~/.kube/config

### Download and Install kfctl

    wget https://github.com/kubeflow/kubeflow/releases/download/v0.6.2/kfctl_v0.6.2_linux.tar.gz
    tar xf kfctl_v0.6.2_linux.tar.gz
    sudo mv kfctl /snap/bin/

### Configure and Setup Kubeflow

    kfctl init kubeflow --config=https://raw.githubusercontent.com/kubeflow/kubeflow/v0.6-branch/bootstrap/config/kfctl_k8s_istio.0.6.2.yaml
    cd kubeflow
    kfctl generate all -V
    kfctl apply all -V

See https://github.com/kubeflow/kubeflow/issues/3810 for help running `apply` again.

Get kustomize here: https://github.com/kubernetes-sigs/kustomize/releases/download/v3.1.0/kustomize_3.1.0_linux_amd64.

### Open Dashboard

From the kubeflow VM host, get the IP address of the VM:

    multipass info kubeflow | grep IPv4

Open this URL:

    http://HOST:31380
