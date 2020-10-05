Deployment IBM Security Verify Access Cooklab

Prepare for Access Manager Deployment

Create SCC (Security Context Constraint)

Determine required permissions :

```
$ oc get scc
Error from server (Forbidden): securitycontextconstraints.security.openshift.io is forbidden: User "XXX" cannot list resource "securitycontextconstraints" in API group "security.openshift.io" at the cluster scope
```

The error is telling you that the Role-based Access Control in the cluster doesn't allow the developer user to
list securitycontextconstraints.

This is useful command which can tell you if you can create scc:

```
$ oc auth can-i create scc
Warning: resource 'securitycontextconstraints' is not namespace scoped in group 'security.openshift.io'
no
```

and a command to tell you who can create scc :

```
kmela@GITG07WE097:~/repos/melak-cmd/isva$ oc adm policy who-can create scc
resourceaccessreviewresponse.authorization.openshift.io/<unknown> 

Namespace: isav-lab
Verb:      create
Resource:  securitycontextconstraints.security.openshift.io

Users:  system:admin
        system:serviceaccount:openshift-apiserver-operator:openshift-apiserver-operator
        system:serviceaccount:openshift-apiserver:openshift-apiserver-sa
        system:serviceaccount:openshift-authentication-operator:authentication-operator
        system:serviceaccount:openshift-authentication:oauth-openshift
        system:serviceaccount:openshift-cluster-storage-operator:csi-snapshot-controller-operator
        system:serviceaccount:openshift-cluster-version:default
        system:serviceaccount:openshift-config-operator:openshift-config-operator
        system:serviceaccount:openshift-controller-manager-operator:openshift-controller-manager-operator
        system:serviceaccount:openshift-etcd-operator:etcd-operator       
Groups: system:cluster-admins
        system:masters
```

Login with right user 

```
oc login --token=$TOKEN --server=$SERVER -n isav-lab
```

Or

```
oc login -u system:admin -n isav-lab
```

```
$ oc whoami
xxx
$ oc auth can-i create scc
Warning: resource 'securitycontextconstraints' is not namespace scoped in group 'security.openshift.io'
yes
```

Perform SCC operations

Run the ./perform-scc-operations.sh to perform the following actions:
• Create isva and openldap SA
• Create isva-scc SCC
• Allow isva SA to use the isva-scc SCC
• Allow openldap SA to use the built-in anyuid SCC

```
$ ./deploy/perform-scc-operations.sh 

Create isva & openldap SA
serviceaccount/isva created
serviceaccount/openldap created

Create isva SCC
securitycontextconstraints.security.openshift.io/isva created

Allow isva SA to use the isva SCC
clusterrole.rbac.authorization.k8s.io/system:openshift:scc:isva added: "isva"

Allow openldap SA to use the built-in anyuid SCC
clusterrole.rbac.authorization.k8s.io/system:openshift:scc:anyuid added: "openldap"
```
Import the Access Manager Templates

Import OpenShift Templates which describe the objects required to deploy an Security Verify Access.

The templates will be deployed at the project level as an end user. Login to oc command-line as the developer user

```
oc login --token=$TOKEN --server=$SERVER -n isav-lab
```

Or

```
oc login -u user:developer -n isav-lab
```

Access Manager Core Template

A single instance of this template contains all of the core components required for an Access Manager deployment

This template creates Deployments and Services for the following Access Manager components:
• Configuration container
• Runtime (initially scaled to 0)
• 4 x Distributed Session Cache (all initially scaled to 0)

The template also creates two Secrets:
• A Secret containing the password for the config service admin user
• A Secret containing the information required by non-configuration components to communicate with
the configuration service.

```
```

Access Manager Reverse Proxy Template

One instance of this template is deployed for each Reverse Proxy to be deployed.

This template creates a deployment for an Access Manager Reverse Proxy instance. 

```
```