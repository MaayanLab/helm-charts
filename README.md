# helm-charts

A repository for maayanlab helm charts.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.  Please refer to
Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:
```
helm repo add maayanlab https://maayanlab.github.io/helm-charts
```

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages.  You can then run `helm search repo
maayanlab` to see the charts.

To install some chart:
```
helm install my-app maayanlab/some-chart
```

To uninstall the chart:
```
helm delete my-app
```
