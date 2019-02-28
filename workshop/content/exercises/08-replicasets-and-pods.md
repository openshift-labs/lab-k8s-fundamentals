---
Title: ReplicaSets and Pods
PrevPage: 07-deployment-resource
---

Having created the `deployment` run:

```execute
kubectl get all -o name -l app=blog
```

The output should be similar to:

```
pod/blog-6b8999855c-6jjhj
pod/blog-6b8999855c-zk8pg
deployment.apps/blog
replicaset.apps/blog-6b8999855c
```

Although you only created the `deployment`, this has resulted in the creation of additional resources for `replicaset` and `pod`.

This is because `deployment` acts as a template for the creation of a `replicaset`. A `replicaset` in turn acts as a template for the creation of the `pods`. It is the `pods` which represent the instances of your application. In this case, because the number of replicas has been set to 2, there are 2 pods.

To view the resource definition for the `replicaset` run:

```execute
kubectl get replicaset -l app=blog -o yaml
```

In this you will see that the `spec` section contains:

```
spec:
  replicas: 2
  selector:
    matchLabels:
      app: blog
      pod-template-hash: "896156207"
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: blog
        pod-template-hash: "896156207"
    spec:
      containers:
      - env:
        - name: BLOG_SITE_NAME
          value: OpenShift Blog
        image: openshiftkatacoda/blog-django-py:latest
        imagePullPolicy: Always
        name: blog
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

You can see how this has been filled out with what was provided in the `spec` portion of the `deployment`.

To view the resource definitions for the `pods` run:

```execute
kubectl get pod -l app=blog -o yaml
```

In this case you will see how fields from `spec.template` of the `replicaset` have been used in creating the `pod` definition. The `spec.template` of a `deployment` and `replicaset` is what is referred to as the pod template.

In both `replicaset` and `pod` you will see that a lot of additional fields have been added along the way. This is because they are being filled out with defaults for values which weren't specified in the original `deployment`. The inherited defaults may be from the resource type, but also may be inherited in part from the global or namespace configuration. This is the case for the resource limits on CPU and memory, which have been inherited from limits set for the namespace you are working in.

In any case, if these defaults turn out not to be correct and you need to change them, or you need to add additional settings, they should be added to the `deployment`. You should not edit `replicaset` or `pod` directly yourself. Update the `deployment` instead. The instances of `replicaset` and `pod` will be correspondingly updated for you.

One example of updating the `deployment` is to change the number of replicas, or instances of your application which are running. To effect this change you would need to change the value of `spec.replicas` in the `deployment`.

The `kubectl` command provides an imperative command for updating the number of replicas. To increase the number of replicas to 3, run:

```execute
kubectl scale deployment/blog --replicas 3
```

This will output:

```
deployment.extensions/blog scaled
```

and if you run:

```execute
kubectl get pods -l app=blog
```

you should now see that there are 3 pods running instead of the original 2.

If you ran the command fast enough, you may see a `pod` listed as being in `ContainerCreating` state. This is the new `pod` when it is starting up. Keep running `kubectl get pods` until you see 3 `pods` in the `Running` state.

The problem with having run `kubectl scale` is that the configuration in Kubernetes no longer matches what we used to originally create the `deployment`. This means we wouldn't be able to replicate the application deployment, as it existed in Kubernetes at that point, by deploying the original configuration alone.

To reset the configuration in Kubernetes back to what we had before running `kubectl scale` you can run `kubectl apply` with the original configuration we had in our local configuration file.

```execute
kubectl apply -f frontend-v1/
```

Run:

```execute
kubectl get pods -l app=blog
```

again and you will see that you are back to 2 replicas.

If you ran the command fast enough after applying the original configuration you may see a `pod` listed as being in `Terminating` state. This is the `pod` which is being shutdown in order to bring the number of replicas back to 2. Keep running `kubectl get pods` until you see the number in the `Running` state return back to 2.
