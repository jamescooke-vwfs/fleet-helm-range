# fleet-helm-range
Repo to show issue found using Helm Ranges with Rancher Fleet

## Initialisation

This Helm chart was created using the following steps using version 3.14 of Helm:
```
helm create range-test
```
All of the default resources were then deleted, including all of the values from the helpers template file and the values file.

## Setup

The `namespace.yaml` file was then added, with a range of namespaces within the `values.yaml` file.

In the terminal, this can be tested as follows:
```
cd range-test
helm template . --values ./values.yaml
```
The output is as expected:
```
# Source: range-test/templates/namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ns-1

apiVersion: v1
kind: Namespace
metadata:
  name: ns-2

apiVersion: v1
kind: Namespace
metadata:
  name: ns-3
```

However, when this is added to fleet, only the FINAL namespace in the range (ns-3) will be created.

## Testing via Fleet

Either add a GitRepo manually or import the following YAML into the local cluster.

This was tested using Fleet v0.8 (fleet:102.2.1+up0.8.1).
```yaml
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  annotations:
    field.cattle.io/description: testing helm ranges in fleet
  name: fleet-helm-range
  namespace: fleet-local
spec:
  branch: main
  insecureSkipTLSVerify: false
  paths:
    - /range-test
  repo: https://github.com/jamescooke-vwfs/fleet-helm-range.git
  targets:
    - clusterSelector:
        matchExpressions:
          - key: provider.cattle.io
            operator: NotIn
            values:
              - harvester
```
