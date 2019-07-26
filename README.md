# <img src="https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/openfoam.png" height="80"> Runbook



## Introduction
This runbook is designed to assist in the assessment of the OpenFOAM CFD Software in Oracle Cloud Infrastructure. It automatically downloads and configures OpenFOAM. 
OpenFOAM is the free, open source CFD software released and developed primarily by OpenCFD Ltd since 2004. It has a large user base across most areas of engineering and science, from both commercial and academic organisations. OpenFOAM has an extensive range of features to solve anything from complex fluid flows involving chemical reactions, turbulence and heat transfer, to acoustics, solid mechanics and electromagnetics.

<img align="middle" src="https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/sim.gif" height="180" >

## Architecture
In addition it authenticates all of the nodes on the cluster and creates a common share directory to be used for each of the nodes. A bastion host is created and a cluster of BM.HPC2.36 instances. A public IP is assigned to the network with port 22 open. The Jumpbox can be accessed with the following command:
```
   ssh {username}\@{bm-public-ip-address}
```
Four different types of file storage are created alongside the cluster. One NFS file share is created from the head node's OS disk and shared with all of the compute nodes, /mnt/nfsshare, another share is created on the temp disk of the head node, /mnt/scratch. The temp disk is a physically attached disk and will typically provide faster performance on each of the nodes.
![](https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/images/HPC_arch_draft.png "Architecture for running OpenFOAM in OCI")
## Deployment

Deploying this architecture on OCI can be done in different ways.
* The [resource Manager](https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/Documentation/ResourceManager.md#deployment-through-resource-manager) let you deploy the infrastructure from the console. Only relevant variables are shown but others can be changed in the zip file. 
* [Terraform](https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/Documentation/terraform.md#terraform-installation) is a scripting language for deploying resources. It is the foundation of the Resource Manager, using it will be easier if you need to make modifications to the terraform stack often. 
* The [web console](https://github.com/oci-hpc/oci-hpc-runbook-openfoam/blob/master/Documentation/ManualDeployment.md#deployment-via-web-console) let you create each piece of the architecture one by one from a webbrowser. This can be used to avoid any terraform scripting or using existing templates. 

## Licensing
Since OpenFOAM is open source there are no licensing considerations for this application.
## Running the Application

## Post-processing

## Expected Results

