# docker-compose helm chart

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
