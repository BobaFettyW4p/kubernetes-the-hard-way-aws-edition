Create the Controller VMs
```
for i in 0 1 2; do
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --tag-specification 'ResourceType=instance,Tags=[{Key=name,Value=controller-${i}},{Key=role,Value=controller},{Key=project,Value=kubernetescluster}]'
done
```

Create the worker VMs
```
for i in 0 1 2; do
  aws ec2 run-instances \
  --image-id ami-04a0ae173da5807d3 \
  --instance-type t2.micro \
  --tag-specification 'ResourceType=instance,Tags=[{Key=name,Value=worker{i}},{Key=role,Value=worker}{Key=project,Value=kubernetescluster}]'
```
Retreive all EC2 Instance IDs
```
aws ec2 describe-instances --filters | jq '.Reservations[].Instances[].InstanceId'
``aws ec2 describe-instances --filter Name=tag:Name,Values="${filter}"  --query "Reservations[*].Instances[*].[InstanceId]" --output text
