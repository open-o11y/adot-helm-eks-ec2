# ADOT Helm chart for EKS on EC2 metrics and logs to CW Container Insights
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

This repository contains a [Helm](https://helm.sh/) chart to provide easy to operate end-to-end [AWS Elastic Kubernetes Service](https://aws.amazon.com/eks/) (EKS) on [AWS Elastic Compute Cloud](https://aws.amazon.com/ec2/) (EC2) monitoring with [AWS Distro for OpenTelemetry(ADOT) collector](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html) for metrics and [Fluent Bit](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html) for logs.

The configuration is ready to collect metrics and logs and send them to CloudWatch Container Insights (CWCI).

## Prerequisite

- EKS Cluster on EC2
- IAM Role
- Helm v3+

## Get Repository Information

[Helm](https://helm.sh/) must be installed to use the chart. Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add this repo as follows:
```console
helm repo add [repo_name] https://TO_BE_RELEASED.github.io/adot-helm-eks-ec2
# You can then run following command to see the chart.
helm search repo [repo_name]
```

## Install Chart

```console
$ helm install \
  [RELEASE_NAME] [RELEASE_NAME]/adot-helm-eks-ec2 \
  --set clusterName=[Cluster_Name] --set region=[AWS_Region]
```
Cluster_Name and AWS_Region must be specified with your own EKS cluster and the region.
You can get their names by executing following command.

```console
$ kubectl config current-context

[IAM_User_Name]@[Cluster_name].[AWS_Region].eksctl.io
```

To verify the installation is successfully completed, you can execute the following command.

```console
$ kubectl get pods --all-namespaces

# If you see these four pods, two for fluent-bit and two for ADOT Collector as Daemonset
# running with your existed pods, they are succesfully installed.
  
NAMESPACE                NAME                             READY   STATUS    RESTARTS   AGE
amazon-cloudwatch        fluent-bit-f27cz                 1/1     Running   0          4s
amazon-cloudwatch        fluent-bit-m2mkr                 1/1     Running   0          4s
aws-cloudwatch-metrics   adot-collector-daemonset-7nrst   1/1     Running   0          4s
aws-cloudwatch-metrics   adot-collector-daemonset-x7n8x   1/1     Running   0          4s
```

### Verify whether the metrics and logs were sent to CloudWatch

- Open CloudWatch console
- Select "Logs -> Log groups" on the left navigation bar.
- Check if following four folder is existed. (performance will take longer than others)
```console
/aws/containerinsights/[PROJECT_NAME]/application
/aws/containerinsights/[PROJECT_NAME]/dataplane
/aws/containerinsights/[PROJECT_NAME]/host
/aws/containerinsights/[PROJECT_NAME]/performance
```
- Select "Insights -> Container Insights" on the left navigation bar.
- Choose Performance monitoring in the drop-down menu on the top-left side.
- If you see graphs on the screen, both metrics and logs are successfully working.  

## Configuration
See Customizing the Chart Before Installing. To see all configurable options with detailed comments:

```console
$ helm show values [RELEASE_NAME]/adot-helm-eks-ec2
```

By changing values in `values.yaml`, you are able to customize the chart to use your preferred configuration.
Following options are some useful configuration that can be applied in this Helm chart.

### Optional: Deploy ADOT Collector as Sidecar

Using the `helm install` command guided above is deploying ADOT Collector and Fluentbit as Daemonset.
However, ADOT Collector can be deployed as Sidecar when executing the following command.

```console
$ helm install \
  [RELEASE_NAME] [RELEASE_NAME]/adot-helm-eks-ec2 \
  --set clusterName=[Cluster_Name] --set region=[AWS_Region] \
  --set adotCollector.daemonSet.enabled=false --set adotCollector.sidecar.enabled=true
```
This command disables deploying ADOT Collector as DeamonSet, while enables deploying it as Sidecar.
You can also check whether your applications are successfully deployed by executing the following command.

```console
$ kubectl get pods --all-namespaces

NAMESPACE                NAME                            READY   STATUS    RESTARTS   AGE
adot-sidecar-namespace   adot-sidecar-658dc9ffbb-w9zv2   2/2     Running   0          5m18s
amazon-cloudwatch        fluent-bit-9dcql                1/1     Running   0          5m18s
amazon-cloudwatch        fluent-bit-wqhmd                1/1     Running   0          5m18s
```


### Optional: Deploy ADOT Collector as Deployment and StatefulSet

Deploying ADOT Collector as Deployment mode and StatefulSet mode requires installing ADOT Operator. 
See [OpenTelemetry Operator Helm Chart](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-operator) 
for detailed explanation.

## Uninstall Chart

The following command uninstalls the chart. 
This will remove all the Kubernetes components associated with the chart and deletes the release.

```console
$ helm uninstall [RELEASE_NAME]
```

## Upgrade Chart

```console
$ helm upgrade [RELEASE_NAME] [RELEASE_NAME]/adot-helm-eks-ec2
```

## Further Information

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-EKS-otel.html


## License

<!-- Keep full URL links to repo files because this README syncs from main to gh-pages.  -->
[Apache 2.0 License](https://github.com/prometheus-community/helm-charts/blob/main/LICENSE).