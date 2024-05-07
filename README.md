# OpenShift Virtualization and OpenShift GitOps
Managing virtual machines of OpenShift Virtualization with OpenShift GitOps.

# TL;DR

## Provision the setup
```sh
# GitOps Operator
oc apply -f operators/gitops/operator-gitops.yaml

# OpenShift Virtualization
oc create -f operators/virtualization/operator-virtualization.yaml
# HyperConvergedController CR
oc apply -f operators/virtualization/hyperconverged.yaml

oc get secret/openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d

oc apply -f applicationsets/demo-vm/applicationset-demo-vm.yaml
oc apply -f applicationsets/demo-db/applicationset-demo-db.yaml
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n dev-demo-vm
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n prod-demo-vm
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n dev-demo-db
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n prod-demo-db
```

## Demonstrate route to production VMs:
```sh
oc get vmi -n prod-demo-vm -o custom-columns=VirtualMachineInstance:.metadata.name,Phase:.status.phase
VirtualMachineInstance   Phase
prod-demo-vm-1           Running
prod-demo-vm-2           Running

endpoint=$(oc get route -n prod-demo-vm prod-my-route  -ojsonpath='{.spec.host}')
for i in {1..10}; do curl http://$endpoint; done
This is demo VM 1 :)
This is demo VM 1 :)
This is demo VM 1 :)
This is demo VM 2 :)
This is demo VM 1 :)
This is demo VM 2 :)
This is demo VM 2 :)
This is demo VM 1 :)
This is demo VM 2 :)
This is demo VM 2 :)

```

## Connect from the VM to the database inside a container:
```sh
oc -n dev-demo-db get svc
NAME        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
dev-my-db   ClusterIP   X.X.X.X         <none>        3306/TCP   133m

virtctl -n dev-demo-vm console dev-demo-vm-1

mysqlshow -u developer -pdeveloper -h dev-my-db.dev-demo-db.svc.cluster.local
+--------------------+
|     Databases      |
+--------------------+
| information_schema |
| performance_schema |
| sampledb           |
+--------------------+

exit
```

## Connect from the database pod/container to the VM service:
```sh
endpoint=$(oc get route -n dev-demo-vm dev-my-route  -ojsonpath='{.spec.host}')
dbpod=$(oc -n dev-demo-db get pods -l=app=my-db -oname)
oc -n dev-demo-db exec -it  $dbpod -- curl http://$endpoint
This is demo VM 1 :)
```
