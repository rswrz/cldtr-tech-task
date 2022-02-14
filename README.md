# Tech Task

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
