# openshift-rsyslog
Example configuration for rsyslog in OpenShift 4.

## Usage

1) Create a MachineConfig that configures journald to forward to rsyslog

2) Create the Roles and RoleBindings for the rsyslogd container

3) Deploy the rsyslogd container as a daemonset

4) PROFIT!



