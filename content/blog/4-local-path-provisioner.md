---
title: "How Rancher's Local Path Provisioner works"
date: 2019-12-13
slug: "2020-whats-next"
description: "How local path provisioner works"
keywords: ["Kubernetes", "Storage"]
draft: false
tags: ["Kubernetes", "Storage", "Devops"]
math: false
toc: true
---

For the past month, I've been researching for baremetal k8s distribution to use at our company. I've been using Rancher's [k3s](https://k3s.io), a lightweight kubernetes distribution and I find it very light and convenient to setup. Together with bunch of built-in tools bundled with k3s, I really like its super cool storage provisioner. Thank you Rancher, you guys rocks \m/

## What we'll learn here

- What it takes to dynamically provision a PV in Kubernetes
- What is Local Path Provisioner
- How to use Rancher's Local Path Provisioner
- How dynamic storage **external-provisioner** works

Frankly speaking, I personally find it abit hard for me to get more information about this kind of stuff, unless I work on Kubernetes project itself or storage-related system company. Otherwise, I have to dig around this kind of projects and examine them by myself. I am sharing this to help people like to me to get started to know more about Kubernetes storage and *external-provisioner* specifically.

## How Kubernetes Dynamic Storage Provisioning work

I recommend you to read a [documentation](https://cloud.ibm.com/docs/containers?topic=containers-kube_concepts) from IBM to know in detail how storage works in Kubernetes. 

This is how you attach a storage volume into a pod in Kubernetes:

![Storage provisioner](/blog/4-storage-provisioner.png)

 1. A user creates a PVC
 2. Storage device (storage class) listen to the request and provision a PV
 3. PV is created by the storage provisioner
 4. The PVC and PV are connected together. The status is now `Bound`
 5. The pods created is linked to the PV created

The role of **storage provisioner** here is to help you provision the PV. There are many [provisioners](https://kubernetes.io/docs/concepts/storage/volumes/#types-of-volumes) available out there, both open source and closed source. If you are using managed Kubernetes service (ie. AWS EKS, GKE, AKS), the service provider will likely handle step #2 and #3 for you already so that you do not have to worry about that. They are tightly integrated with their block storage offerings (eg. Google Cloud Persistent Disk, AWS EBS, etc.)

The problem happens when you are using bare-metal Kubernetes cluster where you have to provision and manage the storage by yourself. There are several options available when you're on bare metal setup:

- Use Rook.io or Rancher's Longhorn (thanks again) to setup a clustered storage within the Kubernetes cluster
- Use networked storage cluster running on Ceph or GlusterFS
- Use proprietary solutions like PortWorx, StorageOS, OpenEBS
- Use Local Persistent Volume or HostPath on local node
- many other ways

I was interested to use Local Persistent Volume for my setup because of its speed. There's one caveat when using clustered network storage solution (eg. Ceph/ GlusterFS / Rook.io), the IO performance will drop by ~60% of its original performance because of data replication, but of course you don't have to worry about data loss when the disks/nodes are missing. However, the Local Persistent Volume feature just released in official Kubernetes v1.14 and it **DOES NOT** come as dynamic storage provisoner. I wonder if they working to make it as dynamic storage provisioner in the future.

## Introducing Local Path Provisioner

This is where Local Path Provisioner comes in.  Local Path Provisioner provides its user the convenience to dynamically provision Persistent Volume (PV) based on Persistent Volume Claim (PVC). It utilize the local storage in each node. Based on the user configuration, the Local Path Provisioner will create `hostPath` based persistent volume on the node automatically. It works just fine for my use case for now.

## How to use Local Path Provisioner on your local cluster

Step 1: Install the Local Path Provisioner in your cluster (Skip this if you are using k3s)

``` shell
$ kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

Step 2: Prepare a PVC

``` shell
$ kubectl create -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/examples/pvc.yaml
```

Step 3: Create a `volume-test` pod

``` shell
$ kubectl create -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/examples/pod.yaml
```

Step 4: Check for the PV being created for you

``` shell
# PV is created
$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    CLAIM                    STORAGECLASS   REASON    AGE
pvc-bc3117d9-c6d3-11e8-b36d-7a42907dda78   2Gi        RWO            Delete           Bound     default/local-path-pvc   local-path               4s

# PVC is bounded
$ kubectl get pvc
NAME             STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
local-path-pvc   Bound     pvc-bc3117d9-c6d3-11e8-b36d-7a42907dda78   2Gi        RWO            local-path     16s
```

Step 5: Create a test file in the PV

``` shell
$ kubectl exec volume-test -- sh -c "echo local-path-test > /data/test"
$ kubectl exec volume-test -- sh cat /data/test
> local-path-test
```

Step 6: Check the directory `/var/lib/rancher/k3s/storage` on the node's disk

``` shell
$ ssh <kubernetes node ip>
$ ls /var/lib/rancher/k3s/storage/
> pvc-d82e480f-2929-4717-a275-3d892ab63d4d
$ cat /var/lib/rancher/k3s/storage/pvc-d82e480f-2929-4717-a275-3d892ab63d4d/test 
> local-path-test
```

Tada!! Your file is persisted in the node's disk

Check out official [Github repository](https://github.com/rancher/local-path-provisioner/) for more information about this.

## What happens under the hood

The way Local Path Provisioner works is actually very easy. Generally, Local Path Provisioner implements [kubernete-incubator/external-provisioner](https://github.com/kubernetes-incubator/external-storage) module and they add implementation on top of it.

How Local Path Provisioner is setup:

- A `local-path-provisioner` pod is deployed in your cluster.
- The pod runs a `local-path-provisioner daemon`.
- When it's first initiated, it will load config file from Kubernetes ConfigMap to determine the path where to mount in the node. By default, the value is `/var/lib/rancher/k3s/storage`.
- The daemon will proactively monitor the config if there is any new change.
- The daemon will listen to Kubernetes cluster events using kubernetes-go client.

This workflow of Local Path Provisioner:
![Local Path Provisioner workflow](/blog/4-local-path-provisioner.png)

1. User creates a PVC request. Local Path Provisioner daemon will listen to the Kubernetes events for PVC requests from it's Kubernetes go client
2. The daemon will invoke external-provisioner `controller.go` (refer below)
3. The controller will invoke `Provision()` function in`provisioner.go`  to prepare the volume
4. `Provision()` function will launch a temporary *Helper* pod
  - A busybox container
  - It mounts `/var/lib/rancher/k3s/storage` in the host filesystem
  - It creates a new directory `/var/lib/rancher/k3s/storage/random-id-1` in host filesystem
5.  Once the new directory is ready, it will returns a PersistentVolume object to `controller.go` with path of the new directory and NodeAffinity of the server.
6. Voila! Your PV is ready to be consumed.

*\* `controller.go` refers to `/vendor/github.com/kubernetes-incubator/external-storage/lib/controller/controller.go` in the repository*

You can clearly see them by `tail` the log

``` shell
$ kubectl logs local-path-provisioner-58fb86bdfd-5xhkv
ERROR: logging before flag.Parse: I1213 08:38:32.264357       1 leaderelection.go:187] attempting to acquire leader lease  kube-system/rancher.io-local-path...
ERROR: logging before flag.Parse: I1213 08:38:32.290243       1 leaderelection.go:196] successfully acquired lease kube-system/rancher.io-local-path
ERROR: logging before flag.Parse: I1213 08:38:32.291043       1 controller.go:572] Starting provisioner controller rancher.io/local-path_local-path-provisioner-58fb86bdfd-5xhkv_f1c9359d-1d83-11ea-a159-5aac2052443a!
ERROR: logging before flag.Parse: I1213 08:38:32.292231       1 event.go:221] Event(v1.ObjectReference{Kind:"Endpoints", Namespace:"kube-system", Name:"rancher.io-local-path", UID:"c727ff38-96e8-4535-a8cb-73f8e4959ab3", APIVersion:"v1", ResourceVersion:"392", FieldPath:""}): type: 'Normal' reason: 'LeaderElection' local-path-provisioner-58fb86bdfd-5xhkv_f1c9359d-1d83-11ea-a159-5aac2052443a became leader
ERROR: logging before flag.Parse: I1213 08:38:32.391293       1 controller.go:621] Started provisioner controller rancher.io/local-path_local-path-provisioner-58fb86bdfd-5xhkv_f1c9359d-1d83-11ea-a159-5aac2052443a!
ERROR: logging before flag.Parse: I1213 08:43:56.017931       1 controller.go:927] provision "default/local-path-pvc" class "local-path": started
time="2019-12-13T08:43:56Z" level=info msg="Creating volume pvc-d82e480f-2929-4717-a275-3d892ab63d4d at k3s:/var/lib/rancher/k3s/storage/pvc-d82e480f-2929-4717-a275-3d892ab63d4d" 
ERROR: logging before flag.Parse: I1213 08:43:56.025417       1 event.go:221] Event(v1.ObjectReference{Kind:"PersistentVolumeClaim", Namespace:"default", Name:"local-path-pvc", UID:"d82e480f-2929-4717-a275-3d892ab63d4d", APIVersion:"v1", ResourceVersion:"713", FieldPath:""}): type: 'Normal' reason: 'Provisioning' External provisioner is provisioning volume for claim "default/local-path-pvc"
time="2019-12-13T08:44:18Z" level=info msg="Volume pvc-d82e480f-2929-4717-a275-3d892ab63d4d has been created on k3s:/var/lib/rancher/k3s/storage/pvc-d82e480f-2929-4717-a275-3d892ab63d4d" 
ERROR: logging before flag.Parse: I1213 08:44:18.120204       1 controller.go:1027] provision "default/local-path-pvc" class "local-path": volume "pvc-d82e480f-2929-4717-a275-3d892ab63d4d" provisioned
ERROR: logging before flag.Parse: I1213 08:44:18.120418       1 controller.go:1041] provision "default/local-path-pvc" class "local-path": trying to save persistentvolume "pvc-d82e480f-2929-4717-a275-3d892ab63d4d"
ERROR: logging before flag.Parse: I1213 08:44:18.132018       1 controller.go:1048] provision "default/local-path-pvc" class "local-path": persistentvolume "pvc-d82e480f-2929-4717-a275-3d892ab63d4d" saved
ERROR: logging before flag.Parse: I1213 08:44:18.132069       1 controller.go:1089] provision "default/local-path-pvc" class "local-path": succeeded
ERROR: logging before flag.Parse: I1213 08:44:18.132427       1 event.go:221] Event(v1.ObjectReference{Kind:"PersistentVolumeClaim", Namespace:"default", Name:"local-path-pvc", UID:"d82e480f-2929-4717-a275-3d892ab63d4d", APIVersion:"v1", ResourceVersion:"713", FieldPath:""}): type: 'Normal' reason: 'ProvisioningSucceeded' Successfully provisioned volume pvc-d82e480f-2929-4717-a275-3d892ab63d4d
```

We can see that line 7, the local-path daemon received a request of PVC. That is where the daemon do its job. The rest of the lines are self-explanatory.

Lets inspect the Kubernetes event stream

``` shell
$ kubectl get events
LAST SEEN   TYPE      REASON                    OBJECT                                 MESSAGE
14m         Normal    Starting                  node/k3s                               Starting kubelet.
...
8m44s       Normal    WaitForFirstConsumer      persistentvolumeclaim/local-path-pvc   waiting for first consumer to be created before binding
8m34s       Normal    Provisioning              persistentvolumeclaim/local-path-pvc   External provisioner is provisioning volume for claim "default/local-path-pvc"
8m14s       Normal    ExternalProvisioning      persistentvolumeclaim/local-path-pvc   waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
8m12s       Normal    ProvisioningSucceeded     persistentvolumeclaim/local-path-pvc   Successfully provisioned volume pvc-d82e480f-2929-4717-a275-3d892ab63d4d
<unknown>   Normal    Scheduled                 pod/volume-test                        Successfully assigned default/volume-test to k3s
8m11s       Normal    Pulling                   pod/volume-test                        Pulling image "nginx:stable-alpine"
7m58s       Normal    Pulled                    pod/volume-test                        Successfully pulled image "nginx:stable-alpine"
7m58s       Normal    Created                   pod/volume-test                        Created container volume-test
7m57s       Normal    Started                   pod/volume-test                        Started container volume-test``
```

There is nothing much in the Kubernetes event stream. It's just that you could see how see the process of how the PV is being scheduled, successfully created and bounded.

## How the PV get deleted

I leave it to you to discover yourself. Hint: It's kinda reverse of what we've seen here. Hit me up on [Twitter](https://twitter.com/sdil) if you need more clue :D

-----

This is all what I got after reading the Go code & Kubernetes documentation for few hours. I hope you find it beneficial as I do. If you get creative enough, you might be able to write your own Kubernetes storage external provisioner to suit your need, maybe to invoke Lambda API requests here and there and send you notifications :D

*Disclaimer: I am not associated with Kubernetes community nor the Rancher. If you find this article is false or inaccurate, feel free to reach me.*

If you have any questions or anything to add and to correct me, feel free to reach me on Twitter at [@sdil](https://twitter.com/sdil) :)
