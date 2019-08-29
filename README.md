Lab - Kubernetes Fundamentals
=============================

This repository holds source files for a workshop on Kubernetes fundamentals.

Although the workshop is setup to be run on OpenShift, the workshop content itself is generic to Kubernetes and doesn't cover any OpenShift specific topics. OpenShift, as a Kubernetes distribution, is purely being used as a means to host the workshop.

If you want to review the workshop content, you can browse the files and subdirectories under [workshop/content](workshop/content).

The workshop can be deployed standalone for a single user, or in a multi user environment. In both cases you will require OpenShift. Your OpenShift cluster must support persistent volumes of type ``ReadWriteMany`` (``RWX``). In the case of running a multi user workshop, you will need to have cluster admin access to the OpenShift cluster.

![](terminal.png)

Single user deployment
----------------------

Log in to your OpenShift cluster. You do not need to be cluster admin, but you will need to be able to create a new project, or be a project admin of an existing cluster.

It is recommended you create a new project to deploy the workshop. This will make it easier to cleanup when you have finished, by deleting the entire project.

```
oc new-project workshop
```

The name of the workshop image is:

```
quay.io/openshiftlabs/lab-k8s-fundamentals:1.12
```

To deploy the workshop run:

```
oc new-app https://raw.githubusercontent.com/openshift-labs/workshop-dashboard/master/templates/production.json \
  --param TERMINAL_IMAGE="quay.io/openshiftlabs/lab-k8s-fundamentals:1.12"
```

The template used to deploy the workshop ensures that access to the workshop is secured and is only accessible to users who are listed as admins to the project into which it is deployed.

To get the hostname of the URL for the workshop environment, run:

```
oc get route dc/dashboard
```

Access the workshop environment from your web browser. Accept any self signed certificates. If necessary, you will be redirected to the OpenShift cluster login page. Login using your credentials for that OpenShift cluster. If you are an admin of the project the workshop is deployed in, you will then be redirected back to the workshop environment.

If you deployed the workshop to a fresh project, when done with the workshop, delete the project.

```
oc delete workshop
```

Multi user deployment
---------------------

To deploy the workshop for multiple users, you will need to be a cluster admin of the OpenShift cluster you wish to deploy it to. The deployment will use the learning portal configuration of the workshop spawner. When deployed with this configuration, any user hitting the URL for the workshop will be created a workshop environment on demand. This will be linked to a temporary project and service account. When they have completed the workshop and their session has gone idle, or the maximum session time has expired, their workshop environment and the project will be deleted automatically.

To deploy the workshop for multiple users, run:

```
oc new-app https://raw.githubusercontent.com/openshift-labs/workshop-spawner/master/templates/learning-portal-production.json \
  --param TERMINAL_IMAGE=quay.io/openshiftlabs/lab-k8s-fundamentals:1.12 \
  --param PROJECT_NAME=workshop \
  --param APPLICATION_NAME=portal \
  --param SERVER_LIMIT=8 \
  --param IDLE_TIMEOUT=300 \
  --param MAX_SESSION_AGE=5400 \
  --param RESOURCE_BUDGET=medium
```

The ``PROJECT_NAME`` parameter, must be set to the name of the project into which you are deploying.

The name of the deployment will be ``portal``. This can be changed using the ``APPLICATION_NAME`` parameter.

The ``SERVER_LIMIT`` parameter indicates the maximum number of concurrent workshop sessions that can be created.

The other parameters set timeouts (in seconds) for when a workshop session will be deleted, and quotas on how much resources are permitted to be used for each workshop session.

To clean up everything when the workshop environment is no longer required, run:

```
oc delete all,pvc,sa,rolebinding,secret,configmap,clusterroles,clusterrolebindings -l app=portal-workshop,spawner=learning-portal
```

The ``app`` label should be the combination of the deployment name and the name of the project.
