# FAQs about the operating system and kernel of Container Service {#concept_njg_sys_vdb .concept}

## An error occurs when deleting or updating a container {#section_pfm_tys_vdb .section}

The error that occurs when deleting or updating a container is similar to the following one:

```
failed to remove root filesystem for xxx: device or resource busy
```

Generally, this error occurs because the kernel version of the node where the container resides is low. Log on to the node where the container resides and run the command uname -a to view the kernel version. The error occurs if:

-   The kernel version is equal to or earlier than 3.13 for Ubuntu 14.04.
-   The kernel version is equal to or earlier than 3.10.0-514 for CentOS 7.

**Solutions**

You can upgrade the kernel of the node where the container resides to solve the issue.

1.  Schedule the application from this node by using the scheduling constraint. For more information, see [Specified nodes scheduling](../../../../reseller.en-US/User Guide/Swarm cluster/Applications/Specified nodes scheduling.md#).
2.  Upgrade the kernel version of the node.

    **Ubuntu 14.04**

    ```
    apt-get update && apt-get install -y linux-generic-lts-xenial
    ```

    **CentOS 7**

    ```
    yum update -y kernel
    ```

3.  Restart the node after upgrading the kernel to bring the new version of kernel into effect.
4.  Schedule the application back to this node by using the scheduling constraint.

## Whether or not to configure NTP synchronization for time in containers {#section_vfm_tys_vdb .section}

The time on Linux is obtained by using the kernel interface, and the kernel is shared by containers on the same node. Therefore, time is consistent. Generally, NTP time synchronization is configured on nodes. No additional configurations for NTP synchronization are required in containers.

