# docker-compose helm chart

**DEPRECATED**: I recommend using instead, [u8sand/kube-compose](https://github.com/u8sand/kube-compose) which achieves the same thing but is "safer" (volumes dealt with independent of the chart so that accidental volume deletion is less likely) and provides a bunch of helpers (diffing / drift, and more).

## Upgrading to kube-compose
```bash
# Install kube-compose
pip install kube-compose
# use kube-compose to assess the differences (in the same directory of the docker-compose you used before)
#  there should be little to no differences before you do this upgrade
kube-compose diff
kube-compose drift

# Find & manually delete all secrets corresponding to the helm chart
kubectl get secret --field-selector=type=helm.sh/release.v1
# e.g.
kubectl delete secret/sh.helm.release.v1.test.v1
kubectl delete secret/sh.helm.release.v1.test.v2
# ...

# Install with kube-compose (it will patch any persistent volumes)
kube-compose up
```

## description
This helm chart can be used to run a `docker-compose`-defined application, simply use `docker-compose config` to construct `values.yaml`.

It is highly recommended you prefix your services/volumes in the docker-compose.yaml with the release name to prevent name collisions, we omit this prefix so that dns names work the same in the cluster as they do in the docker-compose.

## usage
```
# initially install a docker-compose app on kubernetes
helm install some-app maayanlab/docker-compose -f <(docker-compose config)

# upgrade a docker-compose app
helm upgrade some-app maayanlab/docker-compose -f <(docker-compose config)

# remove
helm uninstall some-app
```

## docker-compose extensions
Some docker-compose extensions can be used to configure some kubernetes specific features:

```yaml
# ...
services:
  service-name:
    # ...
    x-kubernetes:
      labels:
        # labels annotations go here
      annotations:
        # kubernetes annotations go here
      # ...
      # make a CronJob instead of a Deployment/DaemonSet, set to run at the specified interval
      cron: '* * * * *'
      # opt this service out of the kubernetes deployment
      exclude: true
      # other kubernetes pod spec items
      priorityClassName: my-priority-class
      imagePullPolicy: IfNotPresent
      dnsPolicy: ClusterFirst
      restartPolicy: Always

volumes:
  volume-name:
    # ...
    x-kubernetes:
      mode: ReadWriteOnce
      size: 1Gi
      class: gp2
```
