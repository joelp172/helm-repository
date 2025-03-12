# Docker Hello World Helm Chart

This Helm chart deploys a simple Docker Hello World application on Kubernetes.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- An Ingress Controller (e.g., NGINX) installed in your cluster

## Installing the Chart

To install the chart with the release name `hello-world`:

```bash
helm install hello-world ./hello-world
```

The command deploys the hello-world application on the Kubernetes cluster with default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `hello-world` deployment:

```bash
helm uninstall hello-world
```

## Parameters

The following table lists the configurable parameters of the hello-world chart and their default values.

| Parameter                | Description                                        | Default                        |
|--------------------------|----------------------------------------------------|---------------------------------|
| `replicaCount`           | Number of replicas                                 | `3`                             |
| `image.repository`       | Image repository                                   | `scottsbaldwin/docker-hello-world` |
| `image.tag`              | Image tag                                          | `latest`                        |
| `image.pullPolicy`       | Image pull policy                                  | `IfNotPresent`                  |
| `nameOverride`           | String to partially override chart name            | `""`                            |
| `fullnameOverride`       | String to fully override chart name                | `""`                            |
| `service.type`           | Kubernetes Service type                            | `ClusterIP`                     |
| `service.port`           | Service port                                       | `8088`                          |
| `service.targetPort`     | Port exposed by the container                      | `80`                            |
| `ingress.enabled`        | Enable ingress controller resource                 | `true`                          |
| `ingress.className`      | IngressClass that will be used                     | `nginx-internal`                |
| `ingress.host`           | Hostname to your hello-world installation          | `hello-world.internal`          |
| `ingress.path`           | Path within the host                               | `/`                             |
| `ingress.pathType`       | Ingress path type                                  | `Prefix`                        |
| `ingress.annotations`    | Additional ingress annotations                     | `{nginx.ingress.kubernetes.io/rewrite-target: /}` |
| `resources`              | CPU/Memory resource requests/limits                | `{}`                            |
| `nodeSelector`           | Node labels for pod assignment                     | `{}`                            |
| `tolerations`            | Tolerations for pod assignment                     | `[]`                            |
| `affinity`               | Affinity for pod assignment                        | `{}`                            |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example:

```bash
helm install hello-world joelp172/hello-world \
  --set replicaCount=5 \
  --set ingress.host=hello.example.com
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart:

```bash
helm install hello-world joelp172/hello-world -f values.yaml
```

## Example: Customizing the Hostname

The following example sets a custom hostname for the application:

```bash
helm install hello-world joelp172/hello-world --set ingress.host=hello.example.com
```

## Example: Scaling the Application

To change the number of replicas:

```bash
helm install hello-world joelp172/hello-world --set replicaCount=5
```

## Configuration and Installation Notes

### Ingress Configuration

The application is exposed via an Ingress resource. You need to have an Ingress controller installed in your cluster. The default configuration assumes an NGINX Ingress controller.

### Resource Limits

The default configuration does not set resource limits. For production deployments, it's recommended to set appropriate CPU and memory limits:

```bash
helm install hello-world joelp172/hello-world \
  --set resources.limits.cpu=200m \
  --set resources.limits.memory=256Mi \
  --set resources.requests.cpu=100m \
  --set resources.requests.memory=128Mi
```

## Upgrading the Chart

To update the chart or change configurations after installation:

```bash
helm upgrade hello-world joelp172/hello-world --set ingress.host=new-host.example.com
```

## Uninstall the Chart

```bash
helm uninstall hello-world
```

