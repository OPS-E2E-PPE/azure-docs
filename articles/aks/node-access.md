---
title: Connect to Azure Kubernetes Service (AKS) cluster nodes
description: Learn how to connect to Azure Kubernetes Service (AKS) cluster nodes for troubleshooting and maintenance tasks.
ms.topic: troubleshooting
ms.date: 01/08/2024
ms.reviewer: mattmcinnes
ms.custom: contperf-fy21q4, devx-track-linux
#Customer intent: As a cluster operator, I want to learn how to connect to virtual machines in an AKS cluster to perform maintenance or troubleshoot a problem.
---

# Connect to Azure Kubernetes Service (AKS) cluster nodes for maintenance or troubleshooting

Throughout the lifecycle of your Azure Kubernetes Service (AKS) cluster, you eventually need to directly access an AKS node. This access could be for maintenance, log collection, or troubleshooting operations. 

You access a node through authentication, which methods vary depending on your Node OS and method of connection. You securely authenticate against AKS Linux and Windows nodes using SSH. Alternatively, for Windows Servers you can also connect to Windows Server nodes using the [remote desktop protocol (RDP)][aks-windows-rdp]. 

For security reasons, AKS nodes aren't exposed to the internet. Instead, to connect directly to any AKS nodes, you need to use either `kubectl debug` or the host's private IP address.

This guide shows you how to create a connection to an AKS node and update the SSH key of your AKS cluster.

## Before you begin

To follow along the steps, you need to use Azure CLI that supports version 2.0.64 or later. Run `az --version` to check the version. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

Complete these steps if you don't have an SSH key. Create an SSH key depending on your Node OS Image, for [macOS and Linux][ssh-nix], or [Windows][ssh-windows]. Make sure you save the key pair in the OpenSSH format, avoid unsupported formats such as `.ppk`. Next, refer to [Manage SSH configuration][manage-ssh-node-access] to add the key to your cluster. 

## Linux and macOS

Linux and macOS users can SSH to access their node using `kubectl debug` or their private IP Address. Windows users should skip to the Windows Server Proxy section for a workaround to SSH via proxy.

### SSH using kubectl debug

To create an interactive shell connection, use the `kubectl debug` command to run a privileged container on your node.

1. To list your nodes, use the `kubectl get nodes` command:

    ```bash
    kubectl get nodes -o wide
    ```

    Sample output:

    ```output
    NAME                                STATUS   ROLES   AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE    
    aks-nodepool1-37663765-vmss000000   Ready    agent   166m   v1.25.6   10.224.0.33   <none>        Ubuntu 22.04.2 LTS               
    aks-nodepool1-37663765-vmss000001   Ready    agent   166m   v1.25.6   10.224.0.4    <none>        Ubuntu 22.04.2 LTS              
    aksnpwin000000                      Ready    agent   160m   v1.25.6   10.224.0.62   <none>        Windows Server 2022 Datacenter  
    ```

2. Use the `kubectl debug` command to start a privileged container on your node and connect to it.

    ```bash
    kubectl debug node/aks-nodepool1-37663765-vmss000000 -it --image=mcr.microsoft.com/cbl-mariner/busybox:2.0
    ```

    Sample output:

    ```output
    Creating debugging pod node-debugger-aks-nodepool1-37663765-vmss000000-bkmmx with container debugger on node aks-nodepool1-37663765-vmss000000.
    If you don't see a command prompt, try pressing enter.
    root@aks-nodepool1-37663765-vmss000000:/#
    ```

    You now have access to the node through a privileged container as a debugging pod.

    > [!NOTE]
    > You can interact with the node session by running `chroot /host` from the privileged container.

### Exit kubectl debug mode

When you're done with your node, enter the `exit` command to end the interactive shell session. After the interactive container session closes, delete the debugging pod used with `kubectl delete pod`.

```bash
kubectl delete pod node-debugger-aks-nodepool1-37663765-vmss000000-bkmmx
```

## Private IP Method

If you don't have access to the Kubernetes API, you can get access to properties such as ```Node IP``` and ```Node Name``` through the [AKS Agent Pool Preview API][agent-pool-rest-api] (preview version 07-02-2023 or above) to troubleshoot node-specific issues in your AKS node pools. 

### Create an interactive shell connection to a node using the IP address

For convenience, the nodepools are exposed when the node has a public IP assigned. However, you need to be in the cluster's virtual network to SSH into the node.

1. To get the private IP, use the `machine list` to show all your VMs.

    ```bash
        az aks machine list --resource-group myResourceGroup  --cluster-name myAKSCluster --nodepool-name nodepool1 -o table
     ```

    Sample output:

     ```output
       Name                               Ip 
    ---------------------------------  --------------------------
    aks-nodepool1-33555069-vmss000000  10.224.0.5,family:IPv4;
    aks-nodepool1-33555069-vmss000001  10.224.0.6,family:IPv4;
    aks-nodepool1-33555069-vmss000002  10.224.0.4,family:IPv4;            
    ```
    To target a specific node inside the nodepool, add a `--machine-name` flag:

    ```bash
        az aks machine show --cluster-name myAKScluster --nodepool-name nodepool1 -g myResourceGroup --machine-name aks-nodepool1-33555069-vmss000000 -o table
     ```
    Sample output:

    ```output
        Name                               Ip 
    ---------------------------------  --------------------------
    aks-nodepool1-33555069-vmss000000  10.224.0.5,family:IPv4;
       ```

2. SSH using your private IP address to access your node.

    ```bash
    ssh azureuser@10.224.0.33
    ```

3. Optionally, you can test with Azure Bastion. Follow these steps to set up [Azure Bastion][azure-bastion] to test your connection to your virtual machines using a private IP address. Make sure that the Azure Bastion is hosted in the same virtual network as your VM.

## Windows Server proxy connection

Follow these steps as a workaround to connect with SSH on a Windows Server node.

### Create a proxy server

At this time, you can't connect to a Windows Server node directly by using `kubectl debug`. Instead, you need to first connect to another node in the cluster with `kubectl`, then connect to the Windows Server node from that node using SSH. Alternatively, you can connect to Windows Server nodes using [remote desktop protocol (RDP) connections][aks-windows-rdp].

To connect to another node in the cluster, use the `kubectl debug` command. For more information, follow the above steps in the kubectl section. Create an SSH connection to the Windows Server node from another node, and use the SSH keys provided when you created the AKS cluster and the internal IP address of the Windows Server node.

> [!IMPORTANT]
>
> The following steps for creating the SSH connection to the Windows Server node from another node can only be used if you created your AKS cluster using the Azure CLI and the `--generate-ssh-keys` parameter. The AKS Update command can also be used to manage, create SSH keys on an existing AKS cluster. For more information, see [manage SSH node access][manage-ssh-node-access]. 

Finish the prior steps to use kubectl debug, then return to this section, as you need to run the `kubectl debug` in your proxy.

1. Open a new terminal window and use the `kubectl get pods` command to get the name of the pod started by `kubectl debug`.

    ```bash
    kubectl get pods
    ```

    Sample output:

    ```output
    NAME                                                    READY   STATUS    RESTARTS   AGE
    node-debugger-aks-nodepool1-37663765-vmss000000-bkmmx   1/1     Running   0          21s
    ```

    In the sample output, *node-debugger-aks-nodepool1-37663765-vmss000000-bkmmx* is the name of the pod started by `kubectl debug`.

2. Use the `kubectl port-forward` command to open a connection to the deployed pod:

    ```bash
    kubectl port-forward node-debugger-aks-nodepool1-37663765-vmss000000-bkmmx 2022:22
    ```

    Sample output:

    ```output
    Forwarding from 127.0.0.1:2022 -> 22
    Forwarding from [::1]:2022 -> 22
    ```

    The previous example begins forwarding network traffic from port `2022` on your development computer to port `22` on the deployed pod. When using `kubectl port-forward` to open a connection and forward network traffic, the connection remains open until you stop the `kubectl port-forward` command.

3. Open a new terminal and run the command `kubectl get nodes` to show the internal IP address of the Windows Server node:

    ```bash
    kubectl get no -o custom-columns=NAME:metadata.name,'INTERNAL_IP:status.addresses[?(@.type == \"InternalIP\")].address'
    ```

    Sample output:

    ```output
    NAME                                INTERNAL_IP                       
    aks-nodepool1-19409214-vmss000003   10.224.0.8  
    ```

    In the previous example, *10.224.0.62* is the internal IP address of the Windows Server node.

4. Create an SSH connection to the Windows Server node using the internal IP address, and connect to port `22` through port `2022` on your development computer. The default username for AKS nodes is *azureuser*. Accept the prompt to continue with the connection. You're then provided with the bash prompt of your Windows Server node:

    ```bash
    ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' azureuser@10.224.0.62
    ```

    Sample output:

    ```output
    The authenticity of host '10.224.0.62 (10.224.0.62)' can't be established.
    ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
    Are you sure you want to continue connecting (yes/no)? yes
    ```

    > [!NOTE]
    > If you prefer to use password authentication, include the parameter `-o PreferredAuthentications=password`. For example:
    >
    > ```console
    >  ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' -o PreferredAuthentications=password azureuser@10.224.0.62
    > ```

## Next steps

If you need more troubleshooting data, you can [view the kubelet logs][view-kubelet-logs] or [view the Kubernetes control plane logs][view-control-plane-logs].

To learn about managing your SSH keys, see [Manage SSH configuration][manage-ssh-node-access].

<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-control-plane-logs]: monitor-aks-reference.md#resource-logs
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[azure-bastion]: ../bastion/bastion-overview.md 
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[agent-pool-rest-api]: /rest/api/aks/agent-pools/get#agentpool
[manage-ssh-node-access]: manage-ssh-node-access.md

