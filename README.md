# containerinfo Helm chart

## Description

This Helm chart deploys [containerinfo](https://github.com/guerzon/containerinfo), which extracts some information from running containers in the current Kubernetes cluster where it is deployed to, using a query string passed to it.

## Prerequisites

1. Access to an existing Kubernetes cluster, tested with `v1.24.7`.
2. Helm binary installed locally, tested with `v3.11.0`.

## Usage

```bash
# optionally create a namespace
kubectl create ns containerinfo

# install the chart
helm [-n containerinfo] upgrade -i containerinfo .

# uninstall the chart
helm [-n containerinfo] uninstall containerinfo
```

### Exposure

By default, the Helm chart will only create a service called `containerinfo`:

```bash
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
containerinfo   ClusterIP   172.19.28.53   <none>        80/TCP    9m8s
```

If you have [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/) installed on your cluster and a TLS secret, you can also pass custom variables as follows:

```bash
cat <<EOF > custom.yaml
ingress:
  enabled: true
  hostname: containerinfo.contoso.com
  tlsSecret: containerinfo.contoso.com-tls
EOF

helm [-n containerinfo] upgrade -i containerinfo . -f demo.yaml
```

### Sample result

```bash
lester:containerinfo-helm lester$ curl -k -s https://containerinfo.contoso.com/container-resources?pod-label=app.kubernetes.io/component=jenkins-master | jq
[
  {
    "container_name": "jenkins",
    "cpu_limit": "1",
    "cpu_req": "500m",
    "mem_limit": "2Gi",
    "mem_req": "500Mi",
    "namespace": "devops-tools",
    "pod_name": "jenkins-6d4476cb47-wlw9m"
  }
]
lester:containerinfo-helm lester$
```

## Parameters

### General settings

| Name                | Description                              | Value                   |
| ------------------- | ---------------------------------------- | ----------------------- |
| `image.registry`    | containerinfo image registry             | `docker.io`             |
| `image.repository`  | containerinfo image repository           | `guerzon/containerinfo` |
| `image.tag`         | containerinfo image tag                  | `latest`                |
| `image.pullPolicy`  | containerinfo image pull policy          | `IfNotPresent`          |
| `image.pullSecrets` | Specify docker-registry secret names     | `[]`                    |
| `fullnameOverride`  | String to override the application name. | `""`                    |


### Security settings

| Name                  | Description                           | Value             |
| --------------------- | ------------------------------------- | ----------------- |
| `serviceAccount.name` | Name of the service account to create | `containerinfosa` |


### Exposure Parameters

| Name                              | Description                                                                    | Value                       |
| --------------------------------- | ------------------------------------------------------------------------------ | --------------------------- |
| `ingress.enabled`                 | Deploy an ingress resource.                                                    | `false`                     |
| `ingress.class`                   | Ingress resource class                                                         | `nginx`                     |
| `ingress.nginxIngressAnnotations` | Add nginx specific ingress annotations                                         | `true`                      |
| `ingress.additionalAnnotations`   | Additional annotations for the ingress resource.                               | `{}`                        |
| `ingress.tls`                     | Enable TLS on the ingress resource.                                            | `true`                      |
| `ingress.hostname`                | Hostname for the ingress.                                                      | `containerinfo.contoso.com` |
| `ingress.path`                    | Default application path for the ingress                                       | `/`                         |
| `ingress.pathType`                | Path type for the ingress                                                      | `ImplementationSpecific`    |
| `ingress.pathTypeWs`              | Path type for the ingress                                                      | `ImplementationSpecific`    |
| `ingress.tlsSecret`               | Kubernetes secret containing the SSL certificate when using the "nginx" class. | `""`                        |
| `ingress.nginxAllowList`          | Comma-separated list of IP addresses and subnets to allow.                     | `""`                        |
| `service.type`                    | Service type                                                                   | `ClusterIP`                 |
| `service.annotations`             | Additional annotations for the containerinfo service                           | `{}`                        |


## License

See [License](./LICENSE).

## References

- The source code of the service itself has been published in a separate repo to keep this Helm project cleaner: <https://github.com/guerzon/containerinfo>
- Image has been published in Docker Hub (no CI job yet): <https://hub.docker.com/repository/docker/guerzon/containerinfo/>

### Author

[Lester Guerzon](mailto:guerzon@proton.me)
