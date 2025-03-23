### get VPC name and id 
```bash
 aws ec2 describe-vpcs --query "Vpcs[*].[VpcId, CidrBlock, IsDefault, Tags[?Key=='Name'] | [0].Value]" --output text
```

#### Create VPC 

```bash
#!/usr/bin/env bash

set -e

# Create our vpc

VPC_ID=$(aws ec2 create-vpc \
--cidr-block "172.1.0.0/16" \
--tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=my-vpc-3}]' \
--query Vpc.VpcId \
--output text)

```

### Turn on DNS Hostnames
```bash
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnames "{\"Value\":true}"
```
DNS hostnames refer to the automatic assignment of public or private DNS names to EC2 instances and other resources in your VPC.

If DNS hostnames is enabled in your VPC, AWS assigns DNS names to instances when they are launched.

You can resolve these DNS names to their corresponding IP addresses using DNS resolution 

### DNS Resolution

DNS resolution refers to the ability of resources within your VPC to resolve DNS names (both internal and external) to IP addresses.

 Relationship Between DNS Hostnames and DNS Resolution DNS Hostnames and DNS Resolution work together:

DNS Hostnames assigns DNS names to resources in the VPC.

DNS Resolution allows those DNS names to be resolved into IP addresses.

### create an IGW
```bash
IGW_ID=$(aws ec2 create-internet-gateway \
--query InternetGateway.InternetGatewayId \
--output text
)

echo "IGW_ID: $IGW_ID"
```

### attach an IGW

```bash
aws ec2 attach-internet-gateway --internet-gateway-id $IGW_ID --vpc-id $VPC_ID
```
### create a new subnet
```bash
SUBNET_ID=$(aws ec2 create-subnet \
--vpc-id $VPC_ID \
--cidr-block 172.1.0.0/20 \
--query Subnet.SubnetId \
--output text)

echo "SUBNET_ID: $SUBNET_ID"
```

## auto assign IPv4 addresses
```bash
aws ec2 modify-subnet-attribute --subnet-id $SUBNET_ID --map-public-ip-on-launch
```

# explicty associate subnet
```bash
RT_ID=$(aws ec2 describe-route-tables \
--filters "Name=vpc-id,Values=$VPC_ID" "Name=association.main,Values=true" \
--query "RouteTables[].RouteTableId[]" \
--output text)

echo "RT_ID: $RT_ID"

ASSOC_ID=$(aws ec2 associate-route-table \
--route-table-id $RT_ID \
--subnet-id $SUBNET_ID \
--query AssociationId \
--output text)

echo "ASSOC_ID: $ASSOC_ID"
```

# add a route for our RT to our IGW
```bash
aws ec2 create-route \
--route-table-id $RT_ID \
--destination-cidr-block 0.0.0.0/0 \
--gateway-id $IGW_ID

# Print out delete command
echo "./delete_vpc $VPC_ID $IGW_ID $SUBNET_ID $ASSOC_ID $RT_ID"
```