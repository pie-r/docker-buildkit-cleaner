# docker-buildkit-cleaner

The goal of this repository is described here: _Medium Reference_

This is a temporary workaround to make free space if you are using Docker with the buildkit builder.

In this repository there are two main folders:

- debug: it contains a pod manifest where you need to fill:
  ```
  nodeName: # <NODE_NAME>
  priorityClassName: # <PRIORITY_CLASS> optional
  ```
- charts: contains an helm chart, not really customizable in the values, that create a namespace and a daemonset if `enableCleanup: true`

## Helm chart description

The idea of the helm chart is that:

1. your CD deploy the chart using `values-enable.yaml`
2. you wait some time - 1 hour?
3. your CD deploy the chart using `values.yaml`. This is going to destroy the daemonset and maintain ony the namespace.

The command in the pod is:

```
docker buildx prune --filter until=168h --verbose --force && echo 'buildkit cleanup done - wait to die' && sleep inf
```

It deletes the cache older than 7 days, and wait forever until the CI re-deploy the chart using `values.yaml`

# Useful commands

```sh
$ cd charts
helm template docker-buildkit-cleaner docker-buildkit-cleaner --output-dir docker-buildkit-cleaner/candidate -f docker-buildkit-cleaner/values-enable.yaml
```
