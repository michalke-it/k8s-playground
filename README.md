# Quickly spin up a k8s environment from scratch
This does nothing more than creating a 3 node (1 control + 2 workers) cluster via kubeadm, documented ina  simple Ansible
playbook. Done as part of my CKA preparation.

Based on the official k8s docs, specifically these parts:

* https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cri-o
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#initializing-your-control-plane-node
* https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy

Plus Calico documentation:

* https://docs.tigera.io/calico/latest/getting-started/kubernetes/flannel/install-for-flannel

## Running

```vagrant up```
