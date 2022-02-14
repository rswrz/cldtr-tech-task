# Tech Task

This chart contains a simple Kubernetes deployment of the container image <https://gcr.io/google-samples/hello-app:2.0>. The container image version (image tag) is defined with `appVersion` configuration in [Chart.yaml](./Chart.yaml) and can be overwritten by the parameter `image.tag`.

The Kubernetes deployment creates per default `1` pod replica. This can be configured with the parameter `replicaCount`.

The Kubernetes pods are exposed by default with a Kubernetes service as type `ClusterIP` on port `80`. The service will be kept when uninstalling the chart. This behavior and the service itself is configurable with [parameters](#parameters).

The chart creates per default a `PersistentVolumeClaim` to mount a `PersistentVolume` into the directory `/persistent-data` inside the container for persistent data storage. The usage of an persistent volume can be disabled by setting the parameter `persistence.enabled` to `false` during installation or upgrade.

## Dependencies

This chart uses [bitnami mysql](https://bitnami.com/stack/mysql/helm) as sub-chart (external dependency). Before running `helm install` or `helm upgrade` all dependencies have to be installed into `./charts` directory by running `helm dependency build`.

The [bitnami mysql](https://bitnami.com/stack/mysql/helm) chart needs some mandatory parameters. A list of all parameters is available in the [Github repository](https://github.com/bitnami/charts/tree/master/bitnami/mysql/#parameters).

:warning: All parameters listed in the official bitnami mysql chart documentation have to be prefix with the sub-chart name `mysql`.

Because of the previously mentioned, the following parameters are mandatory for the `tech-task` chart:

| Name                      | Description                                                          |
| ------------------------- | -------------------------------------------------------------------- |
| `mysql.auth.rootPassword` | Password for the `root` user. Ignored if existing secret is provided |
| `mysql.auth.password`     | Password for the new user. Ignored if existing secret is provided    |

Alternatively a external secret can be specified with `mysql.auth.existingSecret`. Please read [MySQL common parameters](https://github.com/bitnami/charts/tree/master/bitnami/mysql/#mysql-common-parameters) for details.

The connection information is passed to the container by environment variables.

| Environment Variable Name | Description                                                                                      |
| ------------------------- | ------------------------------------------------------------------------------------------------ |
| `MYSQL_HOST`              | The database instance hostname (Kubernetes `Service` name; Only accessible inside the namespace) |
| `MYSQL_DATABASE`          | The database name                                                                                |
| `MYSQL_USERNAME`          | The database username                                                                            |
| `MYSQL_PASSWORD`          | The database usernames password                                                                  |

## Quick start

To install the chart in a custom namespace run:

```shell
helm dependency build
helm install tech-task . \
    --create-namespace \
    --namespace tech-task \
    --set mysql.auth.rootPassword=root \
    --set mysql.auth.password=tech-task
```

Upgrade the installation:

```shell
helm upgrade tech-task . \
    --create-namespace \
    --namespace tech-task \
    --set mysql.auth.rootPassword=root \
    --set mysql.auth.password=tech-task
```

Uninstall the chart:

```shell
helm uninstall tech-task
```

Purge kept resources and persistent data:

```shell
kubectl --namespace tech-task delete svc/tech-task
kubectl --namespace tech-task delete pvc/tech-task
```

## Parameters

| Name                       | Description                                                                                                                         | Default Value                                                 |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| affinity                   | Affinity for application pods assignment                                                                                            | `{}`                                                          |
| containerEnv               | Key-Value Map for application pods environment variables                                                                            | `{ _EXAMPLE_ENV_: Just a dummy for demonstration }`           |
| fullnameOverride           | String to fully override `tech-task.names.fullname` template                                                                        | `""`                                                          |
| image.pullPolicy           | Application image pull policy                                                                                                       | `IfNotPresent`                                                |
| image.repository           | Application image repository                                                                                                        | `gcr.io/google-samples/hello-app`                             |
| image.tag                  | Application image tag (immutable tags are recommended)                                                                              | `""` - defaults to `appVersion` in [Chart.yaml](./Chart.yaml) |
| imagePullSecrets           | Specify docker-registry secret names as an array                                                                                    | `[]`                                                          |
| nameOverride               | String to partially override `tech-task.names.fullname` template (will maintain the release name)                                   | `""`                                                          |
| nodeSelector               | Node labels for application pods assignment                                                                                         | `{}`                                                          |
| persistence.accessModes    | Application persistent volume access Modes                                                                                          | `[ ReadWriteOnce ]`                                           |
| persistence.enabled        | Enable persistence on application replicas using a PersistentVolumeClaim. If `false`, use `emptyDir`                                | `true`                                                        |
| persistence.size           | Application persistent volume size                                                                                                  | `10Gi`                                                        |
| persistence.storageClass   | Application persistent volume storage class. If undefined or empty use clusters default storage class.                              | `""`                                                          |
| podAnnotations             | Additional pod annotations for application pods                                                                                     | `{}`                                                          |
| podSecurityContext         | Application pods security context                                                                                                   | `{}`                                                          |
| replicaCount               | Number of application pod replicas                                                                                                  | `1`                                                           |
| resources                  | The resources limits  and requests for application containers                                                                       | `{}`                                                          |
| securityContext            | Application containers security context                                                                                             | `{}`                                                          |
| service.port               | Application Kubernetes service port                                                                                                 | `80`                                                          |
| service.preserve           | Specifies if the Kubernetes service is kept during `helm uninstall`                                                                 | `true`                                                        |
| service.type               | Application Kubernetes service type                                                                                                 | `ClusterIP`                                                   |
| serviceAccount.annotations | Annotations for application service account                                                                                         | `{}`                                                          |
| serviceAccount.create      | Enable the creation of a `ServiceAccount` for application pods                                                                      | `true`                                                        |
| serviceAccount.name        | Name of the created ServiceAccount                                                                                                  | `""`                                                          |
| tolerations                | Tolerations for application pods assignment                                                                                         | `[]`                                                          |
| updateStrategy             | Update strategy type for the application deployment. Set this to `Recreate` if a PV is used that cannot be mounted on multiple pods | `{ type: RollingUpdate, rollingUpdate: {} }`                  |

### Subchat Parameters

A list of all parameters for the `mysql` subchart is available in the [Github repository](https://github.com/bitnami/charts/tree/master/bitnami/mysql/#parameters) of that chart.

:warning: All parameters those parameters needs to be prefix with the chart name `mysql`.

These parameters are set per default in this chart:

| Name                | Description                          | Default Value |
| ------------------- | ------------------------------------ | ------------- |
| mysql.auth.database | Name for a custom database to create | `tech-task`   |
| mysql.auth.username | Name for a custom user to create     | `tech-task`   |
