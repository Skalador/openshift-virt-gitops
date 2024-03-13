# OpenShift Virtualization and OpenShift GitOps
Managing virtual machines of OpenShift Virtualization with OpenShift GitOps.

# TL;DR
```sh
oc apply -f operators/gitops/operator-gitops.yaml
oc create -f operators/virtualization/operator-virtualization.yaml 

oc apply -f operators/virtualization/hyperconverged.yaml 

oc get secret/openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d

oc apply -f applicationsets/demo-vm/applicationset-demo-vm.yaml

oc apply -f applicationsets/demo-vm/applicationset-demo-vm.yaml 
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n dev-demo-vm
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n prod-demo-vm

endpoint=$(oc get route -n dev-demo-vm dev-my-route  -ojsonpath='{.spec.host}')
for i in {1..10}; do curl http://$endpoint; done
This demo VM 1 :)
This demo VM 1 :)
This demo VM 1 :)
This demo VM 2 :)
This demo VM 1 :)
This demo VM 2 :)
This demo VM 2 :)
This demo VM 1 :)
This demo VM 2 :)
This demo VM 2 :)
```