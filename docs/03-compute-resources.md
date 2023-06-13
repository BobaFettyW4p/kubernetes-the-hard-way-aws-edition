Create the VPC
```
aws ec2 create-vpc \
  --cidr-block 10.0.0.0/24 \
  --tag-specification "ResourceType=vpc,Tags=[{Key=project,Value=kubernetescluster}]"
```
Get the VPC Id of the newly created VPC
```
aws ec2 describe-vpcs \
  --filters Name=tag:project,Values=kubernetescluster \
  | jq -r ".Vpcs[].VpcId"
```

Export the VPC Id to a variable
```
vpc=$(aws ecs describe-vpcs \
  --filters Name=tag:project,Values=kubernetescluster \
  | jq -r ".Vpcs[].VpcId")
```

Create Internet Gateway for the VPC
```
aws ec2 create-internet-gateway --tag-specification "ResourceType=internet-gateway,Tags=[{Key=project,Value=kubernetescluster}]"
```

Retreive the Internet Gateway ID
```
ig=$(aws describe-internet-gateway --filters Name=tag:project,Values=kubernetescluster | jq -r ".InternetGateways[].InternetGatewayId")
```

Attach the Internet Gateway to the VPC
```
aws ec2 attach-internet-gateway --internet-gateway-id $ig --vpc-id $vpc
```
Confirm the internet gateway was appropriately attached
```
aws ec2 describe-internet-gateway --filters Name=tag:project,Values=kubernetescluster | jq -r ".InternetGateways[].Attachments[]"

#this command will yield somethign similar to this if it is appropriately attached
# {
# "State": "available",
# "VpcId": "vpc-xxxxxxx" where the VPC ID corresponds to the ID of your VPC (confirm with echo $vpc)
# }
```
Create the subnet within the VPC for nodes to reside within
```
aws ec2 create-subnet \
  --vpc-id $vpc \ 
  --cidr-block 10.0.0.0/24 \
  --tag-specification "ResourceType=subnet,Tags=[{Key=project,Value=kubernetescluster}]"
```
create security group to manage routing to external resources
```
aws ec2 create-security-group \
  --group-name allow-external \
  --vpc-id $vpcid \
  --description "Security group to control communication between nodes and outside hosts" \
  --tag-specification "ResourceType=security-group.Tags=[{Key=project,Value=kubernetescluster}]"
```
Retreive Group ID of recently created security group
```
secgroup=$(aws ec2 describe-security-groups \
  --filters Name=group-name,Values=allow-external \
  | jq -r ".SecurityGroups[].GroupId")
```
Create security group rules for the external security group
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


