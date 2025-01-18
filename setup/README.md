# Setup

### [Install Crossplane](https://docs.crossplane.io/latest/software/install/)
```bash
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm install crossplane \
--namespace crossplane-system \
--create-namespace crossplane-stable/crossplane 
```

### [Install a common config for providers that enables logs](https://docs.crossplane.io/latest/guides/troubleshoot-crossplane/#provider-logs)
ControllerConfig has been deprecated, replace it with [DeploymentRuntimeConfig](https://docs.crossplane.io/latest/concepts/providers/#runtime-configuration).
```bash
kubectl apply -f deployment-runtime-config-debug.yaml
```

### [Install Openstack provider](https://github.com/crossplane-contrib/provider-openstack?tab=readme-ov-file#getting-started)
```bash
kubectl apply -f provider-openstack.yaml
```

### [Configure Openstack provider](https://github.com/crossplane-contrib/provider-openstack?tab=readme-ov-file#configuration)
Create `openstack-auth-config.json` in the current directory, provide data necessary to authenticate. \
Supported fields can be found in the [source code](https://github.com/crossplane-contrib/provider-openstack/blob/main/internal/clients/openstack.go), check explanation of their roles in the [Terraform docs](https://registry.terraform.io/providers/terraform-provider-openstack/openstack/latest/docs#configuration-reference). \
I recommend to attempt to reproduce your Openstack RC File's configuration, for example:
```json
{
  "auth_url": "",
  "tenant_id": "",
  "user_domain_name": "",
  "region": "",
  "user_name": "",
  "password": "",
  "insecure": ""
}
```
Authentication can be configured differently on the Openstack's end so the required fields may differ.

Create an auth Secret.
```bash
kubectl --namespace crossplane-system create secret generic openstack-auth-config --from-file=config=openstack-auth-config.json
```

Create a ProviderConfig which points to the auth Secret.
```bash
kubectl apply -f provider-config-openstack.yaml
```

### [Install Kubernetes provider](https://github.com/crossplane-contrib/provider-kubernetes?tab=readme-ov-file#install)
```bash
kubectl apply -f provider-kubernetes.yaml
```

### [Configure Kubernetes provider](https://github.com/crossplane-contrib/provider-kubernetes/blob/main/examples/provider/config.yaml)
Create `kubeconfig` Secret.
```bash
KUBECONFIG_PATH=~/.kube/config
kubectl --namespace crossplane-system create secret generic kubeconfig --from-file=kubeconfig=$KUBECONFIG_PATH
```

Create a ProviderConfig which points to the `kubeconfig` Secret.
```bash
kubectl apply -f provider-config-kubernetes.yaml
```

### Install [dummy-message-service](https://github.com/proman3419/dummy-message-service)
```bash
cd /tmp
git clone git@github.com:proman3419/dummy-message-service.git
kubectl apply -f deploy.yaml
```

Verify that it is running.
```bash
kubectl -n dummy-message-service port-forward svc/dummy-message-service-svc 8000:8000
```

### Install and configure [provider-dummy-message-service](https://github.com/proman3419/provider-dummy-message-service)
Steps in the README.
