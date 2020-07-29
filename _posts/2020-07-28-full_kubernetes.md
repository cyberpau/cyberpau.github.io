---
layout: post
title: Complete Kubernetes Open Source Tutorial
subtitle: Guide on how to setup high availability database, logging, monitoring, disaster recovery.
tags: [kubernetes, paas]
comments: true
---
## Introduction

This article is a compilation of my journey to learning Kubernetes and open source technologies (High Availability MySQL, Redis, Nginx, ElasticSearch, LogStash, Kibana, etc.)

## Summary
1. [Setting up Kubernetes on CentOS 7](#setting-up-kubernetes-on-centos-7)


## Setting up Kubernetes on CentOS 7

In this part, we will be able to learn to setup a simple NAT network for our VMs. We will also learn basic CentOS 7 installation and troubleshooting.


**Requirements**
 - VMWare Workstation (or any Virtualization but instructions may vary)
 - CentOS-7 ISO

**Steps**
1. Setup NAT Virtual Network (VMWare Network Editor)

    In order for our virtual machines to talk to each other, we need a dedicated virtual network. Below is a simple NAT configuration:

        https://github.com/cyberpau/book-of-gists/tree/master/configs/VMWare-Network

    Download the file and import it using VMWare Network Editor. For this example, we will have a starting IP address of `172.16.0.3` - `172.16.0.255`

    ![VMWare Network Sample](../assets/posts/2020-07-28-full-kubernetes.md/vm-network-settings.png)

2. Create a CentOS-7 Virtual Machine

    Install by default and configure the settings as necessary. Make sure we are using the custom network imported.
    
    *See video* [*How to Install CentOS 7 on VMware Workstation 14 Pro?*](https://www.youtube.com/watch?v=LsXQwnnL4bI) *for details*

    Note: For Master Node: atleast 2gb RAM and 2 cores, for Worker Nodes: atleast 1gb RAM and 1 core. on step #1 (vmnet0).

    ![VMWare Settings](../assets/posts/2020-07-28-full-kubernetes.md/vm-settings.png)

    

2. Run the following scripts to install

    In case you are having internet connection issues, please run:
    
        dhclient -v

    Then, please run the script below:

        wget -O - https://raw.githubusercontent.com/cyberpau/book-of-gists/master/automation-scripts/install-k8s-docker-on-centOS-8.sh | bash
    
    Alternatively, you can run the script below. This is the same as above:

        #!/bin/bash
        # Authored by John Paulo Mataac

        if [[ $EUID > 0 ]]
        then echo "Exiting... Please run as root"
        exit
        fi

        ### Update and install prerequisite tools
        yum -y update
        yum -y install net-tools wget telnet yum-utils device-mapper-persistent-data lvm2

        ### Add Docker repository.
        yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

        ### Install Docker CE.
        yum install -y docker-ce-18.06.2.ce

        ### Create /etc/docker directory.
        mkdir /etc/docker

        ### Setup daemon.
        cat > /etc/docker/daemon.json <<EOF
        {
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
        "max-size": "100m"
        },
        "storage-driver": "overlay2",
        "storage-opts": [
        "overlay2.override_kernel_check=true"
        ]
        }
        EOF

        mkdir -p /etc/systemd/system/docker.service.d

        ### Restart Services
        systemctl daemon-reload
        systemctl enable docker
        systemctl restart docker

        ### Disable swap
        swapoff -a
        sed -i 's/^\(.*swap.*\)$/#\1/' /etc/fstab 

        ### load netfilter probe specifically
        modprobe br_netfilter

        ### disable SELinux. If you want this enabled, comment out the next 2 lines. But you may encounter issues with enabling SELinux
        setenforce 0
        sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

        ### Install kubernetes packages
        cat <<EOF > /etc/yum.repos.d/kubernetes.repo
        [kubernetes]
        name=Kubernetes
        baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
        enabled=1
        gpgcheck=1
        repo_gpgcheck=1
        gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
        EOF

        yum -y install kubectl kubelet kubeadm
        systemctl  restart kubelet && systemctl enable kubelet

        ### Enable IP Forwarding
        echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
        cat <<EOF >  /etc/sysctl.d/k8s.conf
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        EOF


        ### Restarting services
        systemctl daemon-reload
        systemctl restart kubelet

        ### Install nfs utils for Kubernetes NFS driver
        yum -y install nfs-utils