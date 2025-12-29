### Connect vm's to the external network using a localnet with a vlan.  
### No pod network with a service and routes like vmware using a dv switch.
### It's a 2 step process.

Step 1.

a. Map a new localnet name.
b. Use the br-ex or any other custom bridge.
c. Using the default ns so it applies to all namespaces or specify a project.

Example:


$ cat 1_br-mapping-vlan1115.yaml
---
apiVersion: nmstate.io/v1
kind: NodeNetworkConfigurationPolicy
metadata:
  name: br-ex-vlan1105
  namespace: default                      ### This is "c" using the default namesapce.
spec:
  nodeSelector:
    node-role.kubernetes.io/worker: ""
  desiredState:
    ovn:
      bridge-mappings:
        - localnet: br-ex-vlan1105        ### This is "a" a new localnet.
          bridge: br-ex                   ### This is "b" using the existing bridge.
          state: present

Step 2.



