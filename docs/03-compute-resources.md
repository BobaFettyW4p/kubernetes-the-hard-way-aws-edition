Create the VPC
```
aws ec2 create-vpc --cidr-block 10.0.0.0/24 --tag-specification "ResourceType=vpc,Tags=[{Key=project,Value=kubernetescluster}]"
```
Get the VPC Id of the newly created VPC
```
aws ec2 describe-vpcs --filters Name=tag:project,Values=kubernetescluster | jq -r ".Vpcs[].VpcId"
```

Export the VPC Id to a variable
```
vpcid=$(aws ecs describe-vpcs --filters Name=tag:project,Values=kubernetescluster | jq -r ".Vpcs[].VpcId")
```

Test that the variable has been appropriately set
```
echo $vpcid

```
Create the subnet within the VPC for nodes to reside within
```
aws ec2 create-subnet --vpc-id $vpcid --tag-specification "ResourceType=subnet,Tags=[{Key=project,Value=kubernetescluster}]" --cidr-block 10.0.0.0/24
```
Create internal security group
```
aws ec2 create-security-group --group-name allow-internal --vpc-id $vpcid --description "Security group to allow inter-node communication" --tag-specification "ResourceType=security-group,Tags=[{Key=project,Value=kubernetescluster}]"
```
Create security group rules for the internal security group
```

```
create external security group
```
aws ec2 create-security-group --group-name allow-external --vpc-id $vpcid --description "Security group to control communication between nodes and outside hosts" --tag-specification "ResourceType=security-group.Tags=[{Key=project,Value=kubernetescluster}]"
```
Create security group rules for the external security group
```

```
create static IP
```

```
Create SSH key pair
```
aws ec2 create-key-pair k8s_key_pair
```

Create SSH key pair and save it
```
aws ec2 create-key-pair --key-name k8s_key_pair | jq -r ".KeyMaterial" > k8s_key_pair.pem
```
Create the Controller VMs
```
for i in 0 1 2; do
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --key-name k8s_key_pair \
  --tag-specification "ResourceType=instance,Tags=[{Key=name,Value=controller-$i},{Key=role,Value=controller},{Key=project,Value=kubernetescluster}]"
done
```

Create the worker VMs
```
for i in 0 1 2; do
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --key-name k8s_key_pair \
  --tag-specification "ResourceType=instance,Tags=[{Key=name,Value=worker-$i},{Key=role,Value=worker},{Key=project,Value=kubernetescluster}]'
done
```
Retreive all EC2 Instance IDs
```
aws ec2 describe-instances --filters | jq '.Reservations[].Instances[].InstanceId'
```

Configure SSH access

```

```


