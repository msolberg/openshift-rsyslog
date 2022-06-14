# openshift-rsyslog
Example configuration for rsyslog in OpenShift 4.

## Usage
1) Create a namespace and a service account for rsyslogd.

```
$ oc create namespace openshift-logging
$ oc create sa logcollector -n openshift-logging
```

2) Create the Roles and RoleBindings for the rsyslogd container

Allow the service account to use the privileged SCC so that ryslogd can read from the journald socket on the host.

```
$ oc create role log-collector-privileged \
    --verb use \
    --resource securitycontextconstraints \
    --resource-name privileged \
    -n openshift-logging                
$ oc create rolebinding log-collector-privileged-binding \
    --role=log-collector-privileged \
    --serviceaccount=openshift-logging:logcollector \
     -n openshift-logging
```

3) Deploy the rsyslogd container as a daemonset

First, edit [openshift/configmap-rsyslogd-config.yaml](openshift/configmap-rsyslogd-config.yaml) if you'd like to change the configuration of rsyslogd.

Then, change the environment variable ```LOGSERVER``` in [openshift/daemonset-rsyslogd.yaml](openshift/daemonset-rsyslogd.yaml) to point to your particular log server.

Finally, deploy the config file and daemonset:

```
$ oc create -f openshift/configmap-rsyslogd-config.yaml -n openshift-logging
$ oc create -f openshift/daemonset-rsyslogd.yaml -n openshift-logging
```


## Deploy Using GitOps

### Provision RHPDS Enviornment
* OCP4 ACM Hub
* OCP4 ACM Managed
* OCP4 ACM Managed

### Prerequisites for deployment

### Install kustomize
[kustomize](https://kubernetes-sigs.github.io/kustomize/installation/)
```
$ curl -s "https://raw.githubusercontent.com/\
kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
$ sudo mv kustomize /usr/local/bin/
```

