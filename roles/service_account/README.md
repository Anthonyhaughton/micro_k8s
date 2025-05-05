# Creating a Service Account for MircoK8s Cluster

## Background
This role creates a SA to be used for all automation going forward. We first use the microk8s config as temp auth to create the SA and SA secret. 

## Extra Info

After Secret is created:
- fetch encoded token:
  ```$ microk8s kubectl -n kube-system get secret ansible-sa-token -o json | jq '.data.token' | tr -d '"'```
- decode value:
  ```$ echo 'token' | base64 -d```
- Store token in vault as a var.

After you should be able to auth into cluster using host and api_key i.e.
```yaml
- name: Create Namespace using SA token
  kubernetes.core.k8s:
    state: present
    definition:
      apiVersion: v1
      kind: Namespace
      metadata:
        name: dev
    host: "{{ k8s_api_host }}" # Vault
    api_key: "{{ k8s_sa_token }}" # Vault
    validate_certs: false
```
