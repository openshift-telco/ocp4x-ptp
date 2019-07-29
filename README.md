# OCP 4.x support for PTP

Linuxptp is an implementation of the Precision Time Protocol (PTP) according to IEEE standard 1588v2 for Linux. This is a DaemonSet to support PTP in OCP4.x.

## Installing

- Apply MachineConfig to load `ptp` Kernel module
    ```
    # Note: Update to match the worker type. By default using 'worker-rt'
    oc create -f ./assets/manifests/00_98-worker-rt-ptp.yaml
    ```

- Build or download the DaemonSet Container and upload to local container repo
    To build the image:
    ```
    # NOTE: When using the UBI image a RHEL node with valid subscription is required for the linuxptp package
    # if not in a RHEL node, use the Dockerfile.fedora can be used.
    podman build -t bastion.example.com:5000/ocp4x/ptp:4.1 -f Dockerfile.ubi
    podman push bastion.example.com:5000/ocp4x/ptp:4.1
    ```
    
    To download the image:
    ```
    podman pull quay.io/redhat/ptp
    podman tag quay.io/redhat/ptp bastion.example.com:5000/ocp4x/ptp:4.1
    podman push bastion.example.com:5000/ocp4x/ptp:4.1
    ```

- Create namespace and RBACs
    ```
    oc create -f ./assets/manifests/01_namespace.yaml
    oc create -f ./assets/manifests/02_serviceaccount.yaml
    oc create -f ./assets/manifests/03_rbac.yaml
    ```

- Update DaemonSet ConfigMap and Deploy DaemonSet
    ```
    # Edit ConfigMap with the required options
    # For valid options -- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-using_ptp
    vi ./assets/manifests/04_daemonset.yaml
    oc create -f ./assets/manifests/04_daemonset.yaml
    ```

- Testing PTP sync - in one of the nodes execute
    ```
    chronyc sources
    ```

# Credits

Some PTP reference ideas from:
https://zshisite.wordpress.com/author/zshiredhat/
