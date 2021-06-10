# Helm Chart for cnf and controller

## Pre-condition
**1.Install cert-manager**
 
`kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml`
 
## Steps to install CNF and CRD Controller
**1.Create namespace for SDEWAN Central Controller v1Microservices**

`kubectl create namespace sdewan-system`
 
**2.Generate certificate for cnf**

`kubectl apply -f cert/cnf_cert.yaml`
 
**3.Install CNF**

```
helm package sdewan_cnf
helm install sdewan_cnf-0.1.0.tgz --generate-name
```

### Note:
- Create ovn-network and provider-network, e.g. 
```
---
apiVersion: k8s.plugin.opnfv.org/v1alpha1
kind: ProviderNetwork
metadata:
  name: pnetwork
spec:
  cniType: ovn4nfv
  ipv4Subnets:
  - subnet: 10.10.20.1/24
    name: subnet
    gateway: 10.10.20.1/24
    excludeIps: 10.10.20.2..10.10.20.9
  providerNetType: VLAN
  vlan:
    logicalInterfaceName: eno1.100
    providerInterfaceName: eno1
    vlanId: "100"
    vlanNodeSelector: all

---
apiVersion: k8s.plugin.opnfv.org/v1alpha1
kind: Network
metadata:
  name: ovn-network
spec:
  # Add fields here
  cniType: ovn4nfv
  ipv4Subnets:
  - subnet: 172.16.30.1/24
    name: subnet1
    gateway: 172.16.30.1/24
```
- Update `helm/sdewan_cnf/values.yaml` to configure the network information

**4.Install CRD controller**

```
helm package sdewan_controllers
helm install sdewan_controllers-0.1.0.tgz --generate-name
```

