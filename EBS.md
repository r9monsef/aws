create a EBS srtorage as a new hard disk and attach to the EC2 instanse 

```txt
AWS-----> EC2 dashborad ----> volumes ----> crate a new volume ----> ok

note: the EBS and target EC2 sholud be in same AZ 

after cration go to volume part and right click on the EBS and attach to the instance 
```

after attach the EBS to the EC2 we can crate a partion and create file system and mount to the server 

```bash
in ubuntu server 

fdisk /dev/xvdn 
and crete new partition

and mount partition to the diseired directory

```