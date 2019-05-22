# <img src="https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/openfoam.png" height="80"> Runbook



## Introduction
This runbook is designed to assist in the assessment of the OpenFOAM CFD Software in Oracle Cloud Infrastructure. It automatically downloads and configures OpenFOAM. 
OpenFOAM is the free, open source CFD software released and developed primarily by OpenCFD Ltd since 2004. It has a large user base across most areas of engineering and science, from both commercial and academic organisations. OpenFOAM has an extensive range of features to solve anything from complex fluid flows involving chemical reactions, turbulence and heat transfer, to acoustics, solid mechanics and electromagnetics.

<img src="https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/sim.gif" height="240" align="middle">

## Architecture
In addition it authenticates all of the nodes on the cluster and creates a common share directory to be used for each of the nodes. A bastion host is created and a cluster of BM.HPC2.36 instances. A public IP is assigned to the network with port 22 open. The Jumpbox can be accessed with the following command:
```
   ssh {username}\@{bm-public-ip-address}
```
Four different types of file storage are created alongside the cluster. One NFS file share is created from the head node's OS disk and shared with all of the compute nodes, /mnt/nfsshare, another share is created on the temp disk of the head node, /mnt/scratch. The temp disk is a physically attached disk and will typically provide faster performance on each of the nodes.
![](https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/HPC_arch_draft.png "Architecture for running OpenFOAM in OCI")
## Deployment

## Installation
A number of packages are installed during deployment in order to support the NFS share and the tools that are used to create the authentication. During the authentication phase of the deployment, files named nodenames.txt and nodeips.txt are placed in ~/bin. Each of these nodes should be accesible with the following command:
```
   ssh {username}\@{bm-public-ip-address}
```
In addition OpenFOAM version 2.3.1 is installed into the /mnt/resource/scratch/applications/ directory and the path to the OpenFOAM binary is added to ~.bashrc. The benchmark model that was selected at deploy time is downloaded and unpacked. It is placed in /mnt/resource/scratch/benchmark on the bastion. 
## Licensing
Since OpenFOAM is open source there are no licensing considerations for this application.
## Running the Application

## Post-processing

## Expected Results

