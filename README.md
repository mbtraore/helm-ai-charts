# Open WebUI Helm

![Version: 0.1.2](https://img.shields.io/badge/Version-0.1.2-informational?style=flat-square) ![AppVersion: 0.1.107](https://img.shields.io/badge/AppVersion-0.1.107-informational?style=flat-square)

Chart to deploy Open WebUI, a ChatGPT-Style Web UI Client for Ollama 🦙.

![Open WebUI Demo](https://github.com/ollama-webui/ollama-webui/blob/main/demo.gif?raw=true)

## TL;DR

```sh
helm repo add braveokafor https://braveokafor.github.io/helm-charts/
helm install ollama braveokafor/open-webui
```

#### OR:

```sh
helm repo add braveokafor https://charts.braveokafor.com
helm install ollama braveokafor/open-webui
```

## Introduction

Deploy [Open WebUI](https://github.com/ollama-webui/ollama-webui) on Kubernetes easily with this Helm chart.

It enables you to run a ChatGPT-style web UI client, with a variety of open-source large language models available at [ollama.ai/library](ollama.ai/library). 

Alternatively, you can also also run against the OpenAI API or LiteLLM.

## Source Code

* <https://github.com/jmorganca/ollama/>
* <https://github.com/open-webui/open-webui>
* <https://github.com/braveokafor/open-webui-helm/>

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| https://charts.braveokafor.com | ollama | 0.1.x |

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| braveokafor | <brave@braveokafor.com> | <https://braveokafor.com/> |

## Installing the Chart

To install the chart with the release name `ollama`:

```sh
helm install ollama braveokafor/open-webui
```

The command deploys Ollama on the Kubernetes cluster in the default configuration.

The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `ollama` deployment:

```sh
helm delete ollama
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration and installation details

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install ollama \
  --set ollama.gpu.enabled=true \
    braveokafor/open-webui
```
The above command enables GPU support for Ollama.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example:

```console
helm install ollama -f values.yaml braveokafor/open-webui
```

Example fully configured `values.yaml` to setup with Ollama:

```yaml
# values.yaml
ollama:
  enabled: true
  ollama:
    gpu:
      enabled: true
    models:
      - gemma
      - mistral
  nodeSelector:
    cloud.google.com/gke-accelerator: "nvidia-tesla-t4"
resources:
  requests:
    memory: 2048Mi
    cpu: 1000m
ingress:
  enabled: true
  hostname: ollama.braveokafor.com
  annotations:
    kubernetes.io/ingress.global-static-ip-name: ollama
    ingressClassName: gce
  tls:
    enabled: true
```

Example fully configured `values.yaml` to setup with OpenAI:

```yaml
# values.yaml
openai:
  enabled: true
  apiKey: "DX0mvZ6hQvUpM3FSL9qj58KTsbFwPYNsvgT0ztAiYA2q-eWsrxoQ"
ollama:
  enabled: false
resources:
  requests:
    memory: 2048Mi
    cpu: 1000m
ingress:
  enabled: true
  hostname: ollama.braveokafor.com
  annotations:
    kubernetes.io/ingress.global-static-ip-name: ollama
    ingressClassName: gce
  tls:
    enabled: true
```

Example fully configured `values.yaml` to setup with LiteLLM:

```yaml
# values.yaml
litellm:
  enabled: true
  config:
    model_list:
      - model_name: gpt-3.5
        litellm_params:
          model: gpt-3.5-turbo
          api_base: https://api.openai.com/v1
          api_key: "Ec2CWgmxnJRhdMSIlkwZbrFpxDJnuzSUHnVVDpUUaM62U4NavRIL"
ollama:
  enabled: false
resources:
  requests:
    memory: 2048Mi
    cpu: 1000m
ingress:
  enabled: true
  hostname: ollama.braveokafor.com
  annotations:
    kubernetes.io/ingress.global-static-ip-name: ollama
    ingressClassName: gce
  tls:
    enabled: true
```

## Overrides

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| fullnameOverride | string | `""` | String to fully override `"webui"` |
| nameOverride | string | `"webui"` | Provide a name in place of `webui` |

## Ollama parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| ollama.enabled | bool | `true` | Install Ollama Helm Chart |
| ollama.externalURL | string | `""` | External Ollama API URL (Can be used even if Ollama is not enabled/ installed) |

## OpenAI parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| openai.apiKey | string | `""` | OpenAI API Key |
| openai.baseUrl | string | `"https://api.openai.com/v1"` | OpenAI API API URL |
| openai.enabled | bool | `false` | Inject OpenAI API Environment Variables |
| openai.existingApiKeySecret | string | `""` | Name of an existing secret resource containing the OpenAI API credentials |
| openai.existingApiKeySecretKey | string | `"openai-api-key"` | Name of the existing secret's key containing the OpenAI API credentials |

## LiteLLM parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| litellm.config | object | `[]` (See [values.yaml]) | LiteLLM `config.yaml` |
| litellm.enabled | bool | `false` | Mount LiteLLM `config.yaml` |

## Image parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| image.imagePullPolicy | string | `"IfNotPresent"` | Image pull policy for Open WebUI |
| image.repository | string | `"ghcr.io/open-webui/open-webui"` | Repository to use for Open WebUI |
| image.tag | string | `"v0.1.107"` | Tag to use for Open WebUI |
| imagePullSecrets | list | `[]` | Secrets with credentials to pull images from a private registry |

## General parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Assign custom [affinity] rules to the deployment |
| autoscaling.behavior | object | `{}` | Configures the scaling behavior of the target in both Up and Down directions. |
| autoscaling.enabled | bool | `false` | Enable Horizontal Pod Autoscaler ([HPA]) for Open WebUI |
| autoscaling.maxReplicas | int | `5` | Maximum number of replicas for Open WebUI [HPA] |
| autoscaling.metrics | list | `[]` | Configures custom HPA metrics for Open WebUI Ref: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/ |
| autoscaling.minReplicas | int | `1` | Minimum number of replicas for Open WebUI [HPA] |
| autoscaling.targetCPUUtilizationPercentage | int | `50` | Average CPU utilization percentage for Open WebUI [HPA] |
| autoscaling.targetMemoryUtilizationPercentage | int | `50` | Average memory utilization percentage for Open WebUI [HPA] |
| containerPorts.http | int | `8080` | webui container port |
| containerSecurityContext | object | See [values.yaml] | Open WebUI container-level security context |
| deploymentAnnotations | object | `{}` | Annotations to be added to webui Deployment |
| deploymentStrategy | object | `{}` | Deployment strategy to be added to the webui Deployment |
| dnsConfig | object | `{}` | [DNS configuration] |
| dnsPolicy | string | `"ClusterFirst"` | Alternative DNS policy for Open WebUI pods |
| env | list | `[]` | Environment variables to pass to Open WebUI |
| envFrom | list | `[]` (See [values.yaml]) | envFrom to pass to Open WebUI |
| extraArgs | list | `[]` | Additional command line arguments to pass to Open WebUI |
| hostAliases | list | `[]` | Mapping between IP and hostnames that will be injected as entries in the pod's hosts files |
| hostNetwork | bool | `false` | Host Network for Open WebUI pods |
| lifecycle | object | `{}` | Specify postStart and preStop lifecycle hooks for your webui container Specifying this will disable pulling models on container start-up |
| livenessProbe.failureThreshold | int | `3` | Minimum consecutive failures for the [probe] to be considered failed after having succeeded |
| livenessProbe.initialDelaySeconds | int | `10` | Number of seconds after the container has started before [probe] is initiated |
| livenessProbe.periodSeconds | int | `10` | How often (in seconds) to perform the [probe] |
| livenessProbe.successThreshold | int | `1` | Minimum consecutive successes for the [probe] to be considered successful after having failed |
| livenessProbe.timeoutSeconds | int | `1` | Number of seconds after which the [probe] times out |
| nodeSelector | object | `{}` | [Node selector] |
| pdb.annotations | object | `{}` | Annotations to be added to Open WebUI pdb |
| pdb.enabled | bool | `false` | Deploy a [PodDisruptionBudget] for Open WebUI |
| pdb.labels | object | `{}` | Labels to be added to Open WebUI pdb |
| pdb.maxUnavailable | string | `""` | Number of pods that are unavailable after eviction as number or percentage (eg.: 50%). |
| pdb.minAvailable | string | `""` (defaults to 0 if not specified) | Number of pods that are available after eviction as number or percentage (eg.: 50%) |
| podAnnotations | object | `{}` | Annotations to be added to webui pods |
| podLabels | object | `{}` | Labels to be added to webui pods |
| priorityClassName | string | `""` | Priority class for Open WebUI pods |
| readinessProbe.failureThreshold | int | `3` | Minimum consecutive failures for the [probe] to be considered failed after having succeeded |
| readinessProbe.initialDelaySeconds | int | `10` | Number of seconds after the container has started before [probe] is initiated |
| readinessProbe.periodSeconds | int | `10` | How often (in seconds) to perform the [probe] |
| readinessProbe.successThreshold | int | `1` | Minimum consecutive successes for the [probe] to be considered successful after having failed |
| readinessProbe.timeoutSeconds | int | `1` | Number of seconds after which the [probe] times out |
| replicas | int | `1` | The number of webui pods to run |
| resources | object | `{}` | Resource limits and requests for Open WebUI |
| revisionHistoryLimit | int | `3` | Number of old deployment ReplicaSets to retain. The rest will be garbage collected. |
| securityContext | object | `{}` (See [values.yaml]) | Toggle and define pod-level security context. |
| terminationGracePeriodSeconds | int | `30` | terminationGracePeriodSeconds for container lifecycle hook |
| tolerations | list | `[]` | [Tolerations] for use with node taints |
| topologySpreadConstraints | list | `[]` | Assign custom [TopologySpreadConstraints] rules to Open WebUI # Ref: https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/ # If labelSelector is left out, it will default to the labelSelector configuration of the deployment |
| volumeMounts | list | `[]` | Additional volumeMounts to the webui container |
| volumes | list | `[]` | Additional volumes to the webui pod |

## Service parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| service.annotations | object | `{}` | webui service annotations |
| service.externalIPs | list | `[]` | webui service external IPs |
| service.externalTrafficPolicy | string | `""` | Denotes if this Service desires to route external traffic to node-local or cluster-wide endpoints |
| service.labels | object | `{}` | webui service labels |
| service.loadBalancerIP | string | `""` | LoadBalancer will get created with the IP specified in this field |
| service.loadBalancerSourceRanges | list | `[]` | Source IP ranges to allow access to service from |
| service.nodePortHttp | int | `30080` | webui service http port for NodePort service type (only if `webui.service.type` is set to "NodePort") |
| service.servicePortHttp | int | `80` | webui service http port |
| service.servicePortHttpName | string | `"http"` | webui service http port name, can be used to route traffic via istio |
| service.sessionAffinity | string | `""` | Used to maintain session affinity. Supports `ClientIP` and `None` |
| service.type | string | `"ClusterIP"` | webui service type |
| serviceAccount.annotations | object | `{}` | Annotations applied to created service account |
| serviceAccount.automountServiceAccountToken | bool | `true` | Automount API credentials for the Service Account |
| serviceAccount.create | bool | `true` | Create webui service account |
| serviceAccount.labels | object | `{}` | Labels applied to created service account |
| serviceAccount.name | string | `"webui"` | webui service account name |

## Persistence parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| persistence.accessModes | list | `["ReadWriteOnce"]` | Persistent Volume Access Modes |
| persistence.annotations | object | `{}` | Persistent Volume Claim annotations |
| persistence.dataSource | object | `{}` | Custom PVC data source |
| persistence.enabled | bool | `true` | Create a PVC for Open WebUI (empty-dir will be used if not specified) |
| persistence.existingClaim | string | `""` | The name of an existing PVC to use for persistence |
| persistence.selector | object | `{}` | Selector to match an existing Persistent Volume for Open WebUI data PVC # If set, the PVC can't have a PV dynamically provisioned for it |
| persistence.size | string | `"4Gi"` | Size of data volume |
| persistence.storageClass | string | `""` | Storage class of backing PVC |

## Service account parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| serviceAccount.annotations | object | `{}` | Annotations applied to created service account |
| serviceAccount.automountServiceAccountToken | bool | `true` | Automount API credentials for the Service Account |
| serviceAccount.create | bool | `true` | Create webui service account |
| serviceAccount.labels | object | `{}` | Labels applied to created service account |
| serviceAccount.name | string | `"webui"` | webui service account name |

## Ingress parameters

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| ingress.annotations | object | `{}` | Additional ingress annotations |
| ingress.enabled | bool | `false` | Enable an ingress resource for Open WebUI |
| ingress.extraHosts | list | `[]` (See [values.yaml]) | The list of additional hostnames to be covered by ingress record |
| ingress.extraPaths | list | `[]` (See [values.yaml]) | Additional ingress paths |
| ingress.extraRules | list | `[]` (See [values.yaml]) | Additional ingress rules |
| ingress.extraTls | list | `[]` (See [values.yaml]) | Additional TLS configuration |
| ingress.hostname | string | `""` | Open WebUI hostname |
| ingress.ingressClassName | string | `""` | Defines which ingress controller will implement the resource |
| ingress.labels | object | `{}` | Additional ingress labels |
| ingress.path | string | `"/"` | The path to Open WebUI |
| ingress.pathType | string | `"Prefix"` | Ingress path type. One of `Exact`, `Prefix` or `ImplementationSpecific` |
| ingress.tls.enabled | bool | `false` | Enable TLS configuration for the hostname defined at `ingress.hostname` |
| ingress.tls.selfSigned | bool | `false` | Create a Self Signed TLS Certificate for Open WebUI |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs](https://github.com/norwoodj/helm-docs)
