# openshift-rsyslog
Example configuration for rsyslog in OpenShift 4.

## Usage

1) Create a MachineConfig that configures journald to forward to rsyslog for the masters and workers.

```
$ oc apply -f openshift/40-master-custom-journald.yaml
$ oc apply -f openshift/40-worker-custom-journald.yaml
```

  Use the following command to watch the new configuration roll out to the masters.

```
$ oc describe machineconfigpool/master
...
  Degraded Machine Count:     0
  Machine Count:              3
  Observed Generation:        3
  Ready Machine Count:        3
  Unavailable Machine Count:  0
  Updated Machine Count:      3
```

  You should see output similar to this once the change has rolled out:


2) Create a namespace and a service account for rsyslogd.

```
$ oc create namespace openshift-logging
$ oc create sa logcollector -n openshift-logging
```

3) Create the Roles and RoleBindings for the rsyslogd container

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

4) Deploy the rsyslogd container as a daemonset

```
$ oc create -f openshift/configmap-rsyslogd-config.yaml -n openshift-logging
$ oc create -f openshift/daemonset-rsyslogd.yaml -n openshift-logging
```

5) PROFIT!



