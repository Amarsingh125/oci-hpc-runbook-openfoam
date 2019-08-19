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
We will show an example on the OpenFOAM motorbike tutorial and how to tweak the default allrun file to match the architecture that we have built. 

First we will move the folder from the OpenFOAM installer folder. 

```
model_drive=/mnt/block #or /mnt/nvme or /mnt/fss
sudo mkdir $model_drive/work
sudo chmod 775 $model_drive/work
cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
```

In this example, we are just running on 6 cores. Let's adapt this to the cluster we have built. Since creating and modifying cluster are so easy thanks to this guide, you can also make your cluster match the number of processes that you need. If you are just using the free trial, we can use 3 VM.Standard2.4 for a total of 12 cores. If your limits are higher, adapt this to however how many machines you want. 

Edit the file `system/decomposeParDict` and change this line `numberOfSubdomains 6;` to `numberOfSubdomains 12;` or how many processes you will need. 
Then in the hierarchicalCoeffs block, change the n from  `n   (3 2 1);` to `n   (4 3 1);` If you multiply those 3 values, you should get the `numberOfSubdomains`

Edit the Allrun file to look like this. 
```
#!/bin/sh
cd ${0%/*} || exit 1    # Run from this directory
NP=$1
install_drive=/mnt/block #or /mnt/nvme or /mnt/fss
# Source tutorial run functions
. $WM_PROJECT_DIR/bin/tools/RunFunctions

# Copy motorbike surface from resources directory
cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
cp $install_drive/machinelist.txt hostfile

runApplication surfaceFeatures

runApplication blockMesh

runApplication decomposePar -copyZero
echo "Running snappyHexMesh"
mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.patchSummary
ls -d processor* | xargs -I {} rm -rf ./{}/0
ls -d processor* | xargs -I {} cp -r 0 ./{}/0
echo "Running patchsummary"
mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
echo "Running potentialFoam"
mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
echo "Running simpleFoam"
mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam

runApplication reconstructParMesh -constant
runApplication reconstructPar -latestTime

foamToVTK
touch motorbike.foam
```

Now, we are ready to go. Before any run, it is always recommanded to clean the directory using the Allclean script. The argument to the Allrun script is the number of processes and should match `numberOfSubdomains` in the `system/decomposeParDict` file

```
./Allrun 12
```

Watch the magic happen !
## Post-processing

In terms of post-processing, ParaView is a really powerful opensource tool to visualize what is happening. 
Run the commands 
```
foamToVTK
touch motorbike.foam
```

Now open paraview, on GPU instances with a GPU post processing (as in x11vnc), we'll use the latest ParaView version. If you are just a regular VNC, take version 4.4 to avoid any problem with OpenGL libraries. 

Now run

```
ParaViewInstallPath/bin/paraview
```

You may have some error message popping up in the console but you can ignore those. Open the file motorbike.foam that we generated in the run folder and start playing with your model. 

