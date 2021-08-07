# DW Engine

This repository provides a step by step to deploy DW Engine Operator for IBM Cloud Pak For Data on a Red Hat Openshift Container Platform 4.5+.

### Prerequisits

**Softwares**

To install DW Engine Operator, you must have the following softwares installed on your infrastructure:

* Red hat OpenShift Container Platform version 4.5+
* oc cli tool version 4.5+

**Credentials**
* DW Engine container images credentials
* CloudPak for Data (CP4D) instalation credentials

**Supported Archtetures**

DW Engine containers must run on x86_86 cluster nodes.

**Cloud Providers**

DW Engine can run on any Cloud Provider since following resources are available:

- Application or network loadbalancer that can be provisioned on Openshift
- Openshift storage class for database persistence

### Security Considerations

- Always prefer to use encryption in transit and at rest, OpenShift supports TLS on ingress routes and most of cloud providers support encrypted block storages devices.

#### Installation Process

There are few steps to get DW engine up and running on your Red hat Openshift Container Platform

**1.** Create a OCP project for the operator and applications.

```shell
oc new-project <dwengine-project>
```

**2.** Create a secret called dw-engine-image-pull-secret with propper credentials to pull DW Engine container images.

```shell
oc create secret docker-registry dw-engine-image-pull-secret \
    --docker-server=<registry_server> \
    --docker-username=<user_name> \
    --docker-password=<password> \
    --namespace=<dwengine-project>
```

**3.** Create dwengine Custom Resource Definition on OCP cluster;

```shell
oc create -f dw-engine-operator/deploy/crds/drumwave.ia_dwengines_crd.yaml
```

**4.** Create operator's RBAC;

```shell
oc create -f dw-engine-operator/deploy/service_account.yaml
oc create -f dw-engine-operator/deploy/role.yaml
oc create -f dw-engine-operator/deploy/role_binding.yaml
oc create -f dw-engine-operator/deploy/operator.yaml
```

**5.** Create Dwengine Custom Resource yaml file with CP4D url and credentials;

```yaml
apiVersion: drumwave.ia/v1alpha1
kind: DwEngine
metadata:
  name: <custom resource name>
  namespace: <dwengine-project>
spec:
  cp4d_url: <cp4d url>
  cp4d_user: <cp4d user>
  cp4d_password: <cp4d password>
```

**6.** Deploy Dwengine Custom Resource

```shell
oc create -f <manifest>.yaml
```

***7.*** Verify if all pods are up and running
```shell
$ oc get pods
NAME                                                   READY   STATUS    RESTARTS   AGE
dw-engine-operator-6cddc8975b-8gcl4                    1/1     Running   0          12m
example-dwengine-certificate-manager-f669b6557-wv4dr   1/1     Running   0          107s
example-dwengine-gateway-5594b8444-897hw               1/1     Running   0          29m
example-dwengine-mongodb-0                             1/1     Running   0          30m
example-dwengine-rabbitmq-0                            1/1     Running   0          29m
example-dwengine-wkc-manager-75b55b869f-kpvp5          1/1     Running   0          29m
```

***8.*** Get gateway's route url
```shell
$ oc get route
NAME                       HOST/PORT                                                                                                                    PATH   SERVICES                   PORT   TERMINATION     WILDCARD
example-dwengine-gateway   example-dwengine-gateway-dw-engine-tests.<openshift domain>          example-dwengine-gateway   http   edge/Redirect   None
```