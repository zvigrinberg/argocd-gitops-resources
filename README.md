# Argocd Openshift Pipelines - resources

## Installing the Openshift GitOps operator
1. Create a yaml subscription to openshift gitops operator 
```shell
cat > operator/subscription.yaml << EOF
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: openshift-gitops-operator
  namespace: openshift-operators
spec:
  channel: latest 
  installPlanApproval: Automatic
  name: openshift-gitops-operator 
  source: redhat-operators 
  sourceNamespace: openshift-marketplace 
EOF
```

2. Apply it to the cluster
```shell
oc apply -f operator/subscription.yaml
```

3. Wait few seconds , ensure that the operator pod is up and ready:
```shell
oc get pods -n openshift-operators | grep -E 'NAME|gitops-operator'
```

Expected output:
```shell
NAME                                                  READY   STATUS    RESTARTS   AGE
gitops-operator-controller-manager-6f9854d4fb-b6nlg   1/1     Running   0          4m51s
```
4. Check that an ArgoCD Instance was created automatically
```shell
oc get pods -n openshift-gitops
```
Expected Output:
```shell
NAME                                                          READY   STATUS    RESTARTS   AGE
cluster-5d8cc78545-rtqb5                                      1/1     Running   0          4m44s
kam-7dd5f4467f-p4qlm                                          1/1     Running   0          4m43s
openshift-gitops-application-controller-0                     1/1     Running   0          4m41s
openshift-gitops-applicationset-controller-6446555788-wvgn9   1/1     Running   0          4m41s
openshift-gitops-dex-server-c9f8cfcbf-6hxjc                   1/1     Running   0          4m41s
openshift-gitops-redis-bb656787d-69xgz                        1/1     Running   0          4m41s
openshift-gitops-repo-server-56d5b6f555-k4m6m                 1/1     Running   0          4m41s
openshift-gitops-server-67cc4879c9-722v6                      1/1     Running   0          4m41s
```

5. Obtain the password of the `admin` user for Argo CD Instance
```shell
oc get secrets openshift-gitops-cluster -n openshift-gitops -o=jsonpath={.data.'admin\.password'} | base64 -d
```

6. Open Argo CD Instance
```shell
xdg-open https://$(oc get route openshift-gitops-server -n openshift-gitops -o=jsonpath="{.spec.host}")
```

7. Login to The instance using user admin with password obtained in section 5.