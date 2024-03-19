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
oc apply -f applicationsets/demo-db/applicationset-demo-db.yaml 
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n dev-demo-vm
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n prod-demo-vm

endpoint=$(oc get route -n dev-demo-vm dev-my-route  -ojsonpath='{.spec.host}')
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

TBD: 
```sh
ssh -o 'ProxyCommand=virtctl port-forward --stdio=true dev-demo-vm-1.dev-my-route 22' fedora@dev-demo-vm-1.dev-my-route

mysqlshow -u developer -pdeveloper -h dev-my-db
mysqlshow: [Warning] Using a password on the command line interface can be insecure.
+--------------------+
|     Databases      |
+--------------------+
| information_schema |
| performance_schema |
| sampledb           |
+--------------------+
```