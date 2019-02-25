---
layout: post
title: "Kubernetes Glossary"
date: "2019-02-24 11:18"
categories:
    - kubernetes
tags:
    - devops
comments: true
---


### Persistent Volume

A [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) (PV) is a piece of storage in the cluster that has been manually provisioned by an administrator, or dynamically provisioned by Kubernetes using a [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes).

### Persistent Volume Claim

A [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) (PVC) is a request for storage by a user that can be fulfilled by a PV.

PersistentVolumes and PersistentVolumeClaims are independent from Pod lifecycles and preserve data through restarting, rescheduling, and even deleting Pods.
