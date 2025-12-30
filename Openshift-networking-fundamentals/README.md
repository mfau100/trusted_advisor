### Creating a localnet or a secondary network.
### Connect vm's to the external network using a localnet with a vlan.  
### No pod network with a service and routes like vmware using a dv switch.
### It's a 2 step process.

Step 1.

a. Map a new localnet name.
b. Use the br-ex or any other custom bridge.
c. Use the default namespace so it applies to all namespaces or specify a project.

Example:

```
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
```
```
Step 2.

a. Create a nad and specify the name of the localnet to match.
b. Match the namespace.
c. Tag the vlan ID.

$ cat 2_br-localnet-vlan1115.yaml
---
apiVersion: k8s.cni.cncf.io/v1
kind: NetworkAttachmentDefinition
metadata:
  annotations:
    description: Lab Network 172.20.133.0/24 VLAN1105 OVN Bridge
  name: vlan-1105-br-ex
  namespace: default                                    ### This is "b" matches the namespace the localnet is using.
spec:
  config: |-
    {
      "cniVersion": "0.3.1",
      "name": "br-ex-vlan1105",                         ### This is "a" matches the name of the localnet in step 1.
      "type": "ovn-k8s-cni-overlay",
      "topology": "localnet",
      "netAttachDefName": "default/vlan-1105-br-ex",    ### This looks like "b" and the name of the nad.
      "vlanID": 1105                                    ### Tag the correct vlan ID.
    }
```
Step 3

1. Create a vm -> Network Interfaces (tab) -> 3 dots (edit) and replace the Pod networking with the new vlan "default/vlan-1105-br-ex".
2. Use the internal ipam to hand over IP's, but to do it manually.  Go to scripts -> Edit Cloud-init -> In the popup window select "Add network data", in the Ethernet name filed type "eth0" and enter the IP info.  After that you can ping, etc....
