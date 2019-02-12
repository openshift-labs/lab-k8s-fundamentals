---
Title: Accessing the Cluster
PrevPage: 01-the-openshift-platform
NextPage: 03-deploying-an-application
---

For the exercises you will be doing, you will be using the `kubectl` command line program to interact with Kubernetes. This is provided for you via the interactive terminal session accessible through the *Terminal* tab, here in the workshop environment. You do not need to install anything on your own computer. You will be doing everything here through your web browser.

The workshop environment also provides you with a web based view into the Kubernetes cluster. This is available through the *Console* tab of the workshop environment. This web based view uses the web interface which will be included in OpenShift 4.0. This is included so you can visually see the results of what you do in the exercises, but the exercises do not depend on it. The web interface provided here is different to the web interface that may be provided with other Kubernetes distributions.

Before continuing, verify that the `kubectl` command runs and the workshop environment is also functioning. To do this run:

```execute
kubectl version
```

Did you type the command in yourself? If you did, click on the command here instead and you will find that it is executed for you. You can click on any command here in the workshop notes which has the <span class="glyphicon glyphicon-play-circle"></span> icon shown to the right of it, and it will be copied to the interactive terminal and run for you.

When run, you should see output similar to:

```
Client Version: version.Info{Major:"1", Minor:"11", GitVersion:"v1.11.0", GitCommit:"91e7b4fd31fcd3d5f436da26c980becec37ceefe", GitTreeState:"clean", BuildDate:"2018-06-27T20:17:28Z", GoVersion:"go1.10.2", Compiler:"
gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"11+", GitVersion:"v1.11.0+d4cacc0", GitCommit:"d4cacc0", GitTreeState:"clean", BuildDate:"2018-11-09T15:12:26Z", GoVersion:"go1.10.3", Compiler:"gc", Platform:"linux/amd
64"}
```

From this you can see what version of the `kubectl` command you are using, as well as the version of Kubernetes the cluster is running.

You can see the version of Kubernetes the cluster is running, as the workshop environment has already been setup for you such that you are connected to the Kubernetes cluster. This includes having authenticated you with the Kubernetes cluster.

The Kubernetes cluster is a shared environment used by everyone else doing the same workshop. As a result, you have been given a separate identity in the Kubernetes cluster to other users, with your own Kubernetes namespace to work in.

To see details about your current session, run:

```execute
kubectl config current-context
```

The component parts in the output represent, the name of your Kubernetes namespace, the address for the Kubernetes cluster, and the name of the Kubernetes service account your actions will be run as.

The granted roles associated with the service account control what actions you can take in the Kubernetes cluster. You will only be able to take actions allowed for an unprivileged user, you do not have any cluster admin rights.
