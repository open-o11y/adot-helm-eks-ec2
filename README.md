apiVersion: v2
name: adot-helm-eks-ec2
description: adot-helm-eks-ec2 collects metrics using ADOT Collector and logs using Fluentbit agent.
type: application
version: 0.0.1
appVersion: "0.1.0"
kubeVersion: ">=1.16.0-0"
maintainers:
- name: Hyunuk Lim
  email: hyunuk@amazon.com
- name: James Park
  email: jamesjhp@amazon.com
  home: https://github.com/open-o11y/adot-helm-eks-ec2
  icon: https://raw.githubusercontent.com/aws/eks-charts/master/docs/logo/aws.png
  sources:
- https://github.com/open-o11y/adot-helm-eks-ec2