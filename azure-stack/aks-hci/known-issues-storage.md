---
title: Troubleshoot storage issues and errors in Azure Kubernetes Service on Azure Stack HCI 
description: Get help to troubleshoot storage issues and errors in Azure Kubernetes Service on Azure Stack HCI.
author: mattbriggs
ms.topic: troubleshooting
ms.date: 12/13/2021
ms.author: mabrigg 
ms.lastreviewed: 1/14/2022
ms.reviewer: abha

---

# Fix known issues and errors when managing storage

Use this topic to help you troubleshoot and resolve storage-related issues in AKS on Azure Stack HCI.

## Configuring persistent volume claims results in the error: `Unable to initialize agent. Error: mkdir /var/log/agent: permission denied`

This permission denied error indicates that the default storage class may not be suitable for your workloads and occurs in Linux workloads running on top of Kubernetes version 1.19.x or later. Following security best practices, many Linux workloads specify the `securityContext fsGroup` setting for a pod. The workloads fail to start on AKS on Azure Stack HCI since the default storage class does not specify the `fstype (=ext4)` parameter, so Kubernetes fails to change the ownership of files and persistent volumes based on the `fsGroup` requested by the workload.

To resolve this issue, [define a custom storage class](container-storage-interface-disks.md#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk) that you can use to provision PVCs. 

## When creating a persistent volume, an attempt to mount the volume fails
After deleting a persistent volume or a persistent volume claim in an AKS on Azure Stack HCI environment, a new persistent volume is created to map to the same share. However, when attempting to mount the volume, the mount fails, and the pod times out with the error, `NewSmbGlobalMapping failed`.

To work around the failure to mount the new volume, you can SSH into the Windows node and run `Remove-SMBGlobalMapping` and provide the share that corresponds to the volume. After running this command, attempts to mount the volume should succeed.

## Container storage interface pod stuck in a _ContainerCreating_ state
A new Kubernetes workload cluster was created with Kubernetes version 1.16.10, and then updated to 1.16.15. After the update, the `csi-msk8scsi-node-9x47m` pod was stuck in the _ContainerCreating_ state, and the `kube-proxy-qqnkr` pod was stuck in the _Terminating_ state as shown in the output below:

```output
Error: kubectl.exe get nodes  
NAME              STATUS     ROLES    AGE     VERSION 
moc-lf22jcmu045   Ready      <none>   5h40m   v1.16.15 
moc-lqjzhhsuo42   Ready      <none>   5h38m   v1.16.15 
moc-lwan4ro72he   NotReady   master   5h44m   v1.16.15

\kubectl.exe get pods -A 

NAMESPACE     NAME                        READY   STATUS              RESTARTS   AGE 
    5h38m 
kube-system   csi-msk8scsi-node-9x47m     0/3     ContainerCreating   0          5h44m 
kube-system   kube-proxy-qqnkr            1/1     Terminating         0          5h44m  
```

Since `kubelet` ended up in a bad state and can no longer talk to the API server, the only solution is to restart the `kubelet` service. After restarting, the cluster goes into a _running_ state.

## Next steps

- [Troubleshooting overview](troubleshoot-overview.md)
- [Windows Admin Center known issues](known-issues-windows-admin-center.md)