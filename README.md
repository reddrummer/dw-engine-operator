# DW Engine

This repository provides a step by step to deploy DW Engine Operator for IBM Cloud Pak For Data on a Red Hat Openshift Container Platform 4.5+.

### Prerequisits

**Softwares**

To install DW Engine Operator, you must have the following softwares installed on your infrastructure:

|PaaS Softwares:                                    |
|:--------------------------------------------------|
|Red hat OpenShift Container Platform version 4.5+  |
|oc cli tool version 4.5+                           |

|Auxiliar Softwares:|                               |
|:------------------|-------------------------------|
| Mongodb | **inside or outside your OCP Cluster**  |
| RabbtMQ | **inside or outside your OCP Cluster**  |

**Supported Archtetures**

DW Engine containers must run on x86_86 cluster nodes.

**Cloud Providers**

DW Engine can run on any Cloud Provider since following resources are available:

- Application or network loadbalancer that can be provisioned on Openshift
- Openshift storage class if you plan to deploy the Auxiliar Softwares on OCP Cluster.

### Security Considerations

- Always prefer to use encryption in transit and at rest, OpenShift supports TLS on ingress routes and most of cloud providers support encrypted block storages devices.

### Data persistence

If you plan to deploy MongoDB and RabbitMQ on OCP Cluster you must have a storage class configured on OCP with dynamic provisioning of block storage devices.

#### Installation Process

There are few steps to get DW engine up and running on your Red hat Openshift Container Platform

**1.** Create a OCP project for the operator and applications.

```shell
oc new-project <dwengine-project>
```

**2.** Create a secret called DwEngineImagePullSecret with propper credentials to pull applications images.

```shell
oc create secret docker-registry DwEngineImagePullSecret \
    --docker-server=<registry_server> \
    --docker-username=<user_name> \
    --docker-password=<password> \
    --docker-email=<email>  \
    --namespace=<dwengine-project>
```

**3.** Create dwengine Custom Resource Definition on OCP cluster;

```shell
oc create -f dw-engine-operator/deploy/crds/drumwave.ia_dwengines_crd.yaml
```

**4.** Create a dwengine Custom Resource on OCP cluster and related RBAC;

```shell
oc create -f dw-engine-operator/deploy/service_account.yaml
oc create -f dw-engine-operator/deploy/role.yaml
oc create -f dw-engine-operator/deploy/role_binding.yaml
oc create -f dw-engine-operator/deploy/operator.yaml
```
