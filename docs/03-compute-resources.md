Create the VPC
```

```

Create the IP Address Range
```

```

Create internal firewall
```

```

create external firewall
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
  name="controller-${i}"
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --key-name k8s_key_pair \
  --tag-specification "ResourceType=instance,Tags=[{Key=name,Value=$name},{Key=role,Value=controller},{Key=project,Value=kubernetescluster}]"
done
```

Create the worker VMs
```
for i in 0 1 2; do
  name="worker-${i}"
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --key-name k8s_key_pair \
  --tag-specification "ResourceType=instance,Tags=[{Key=name,Value=$name},{Key=role,Value=worker}{Key=project,Value=kubernetescluster}]'
```
Retreive all EC2 Instance IDs
```
aws ec2 describe-instances --filters | jq '.Reservations[].Instances[].InstanceId'
```

Configure SSH access

```

```


