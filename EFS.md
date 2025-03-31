### create a EFS storaga and mount to the EC2 instance 

1-create EFS 

2- edit security group to allow only instances access to the EFS 

3- mount EFS to the EC2 instances 

#### 1-create EFS 
```bash
In the AWS console, search for and create a default EFS.
This is a straightforward service and doesn’t require additional configuration by default.
```

#### 2- add security group to access EC2 

we have a SSH-sg to allow incoming ssh from 0.0.0.0/0 on port 22 

we create a new security-gp with name EFS-sg and open NFS port and in source instead of IP range we add SSH-sg,this means only EC2 that has this sg can access to the EFS.

#### 3-mount EFS to the EC2 instances 

in ubuntu instances :

```bash
apt update && apt install nfs-common 

mkdir  /mnt/efs

sudo mount -t nfs4 fs-xxxxxxxx.efs.YOUR_REGION.amazonaws.com:/ /mnt/efs

```

persist the mounting in sever 

```bash
nano /etc/fstab

fs-xxxxxxxx.efs.YOUR_REGION.amazonaws.com:/ /mnt/efs nfs4 defaults,_netdev 0 0

 mount -a
```