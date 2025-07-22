Kubernetes Cluster upgrades
- Enterprise Zero Downtime
- 3 month (1.32,1.31,1.30)

Prerequisite: 
- Cordon the node (unschedulable)
- Release Notes (chnges from 1.30 --> 1.31) eg.apiGroup for older version & new version should be check
- Downgrade in k8s is impossible. (Fresh installation)
- check with lower env
- Control plane --> Nodes (Same version)
  1.30 --> 1.30
- If you are using Cluster Autoscaller matching with control plane.
- 5 available IP address within in the subnet that u have provided during ur k8s cluster creation.
- kubelet (k8s node) also be match the version of control plane.
  
Process:
a. upgrade the control plane
- Managed k8s Cluster (control plane) does not support that automatic upgrade. We have to use (UI,eksctl,CLI).
- HA of Control Plane
- Keeping ur Control plane in diffrent avablity zone (DR)
- security
- API

b. Upgrade the node group/node plane (1-2hr)
1. Node group (launch template)
- rollout 1by1
2. Manage by your own (Custom Launch Template)
- Cordon
3. Hybrid

c. upgrade the add on : kubeproxy,vpc cni
d. Helm,AgroCD ,prometheus

- OIDC provider is required to communicate pod to with other AWS services.
- 
Demo:
- Go to CLI 
  - eksctl upgrade cluster --name <cluster-name> --version <version-number> --approve
-  Go to Compute --> update the Node group version --> choose upgrdae startegy (Rolling upgrade), Forcefull update
-  Go to the Add ons  --> Upgrade the pulgine


Upgrade test:
1. Function test for QA
2. 
