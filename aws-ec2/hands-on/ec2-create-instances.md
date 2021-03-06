# Prerequisites:

Create and save a keypair. Make sure to set the permissions on this keypair to ready only: `chmod 400 ~/path/to/keypair.pem`. Set the `keypair_name` variable to the name of this keypair.

# Set Required Variables:

`region="us-west-2"`

`subnet_id="subnet-7a94061f"`

`key_name="cjohnson"`

# Create the Gateway EC2 Instance

## Create the Gateway EC2 Instance
The `aws ec2 run-instances` command below will be used to launch the Gateway host - the Gateway host will be used to ssh into other instances in our Amazon infrastructure. The options used when launching the Gateway are described below:

1. Run (meaning startup) an EC2 Instance.
2. Use the "AMI ID" ami-5189a661 - this AMI ID is an Ubuntu Server 14.04 AMI.
3. Use the Instance Type of t2.micro - this is a less expensive but less powerful EC2 instance type.
4. Make the newly run instance a member of the previously created "gateway-$yourname" Security Group.
5. Place this instance in the Subnet ID $subnet_id.
6. Associate a Public IP Address with the EC2 instance at boot.
7. Allow the owner of the key-name keypair to login to the instance.

`gateway_instance_id=$(aws ec2 run-instances --image-id ami-5189a661 --key-name $key_name --region $region --instance-type t2.micro --security-group-ids $gateway_security_group_id --subnet-id $subnet_id --associate-public-ip-address --output text --query 'Instances[*].InstanceId')`

and confirm that the Gateway Instance has been launched:

`echo "The instance ID of the gateway EC2 Instance is: $gateway_instance_id."`

## Tag the Gateway EC2 Instance

We should "tag" instances for future identification - the command below will tag the instance so that we know whom it belongs to.

`aws ec2 create-tags --resources $gateway_instance_id --tags Key="Name",Value="gateway-$your_name" --region $region`

# Create the www EC2 Instance

## Create the www EC2 Instance

`www_instance_id=$(aws ec2 run-instances --image-id ami-5189a661 --key-name $key_name --region $region --instance-type t2.micro --security-group-ids $www_security_group_id $base_security_group_id --subnet-id $subnet_id --associate-public-ip-address --output text --query 'Instances[*].InstanceId')`

and confirm that the www Instance has been assigned an instance id:

`echo "The instance ID of the www EC2 Instance is: $www_instance_id."`

## Tag the www EC2 Instance

`aws ec2 create-tags --resources $www_instance_id --tags Key="Name",Value="www-$your_name" --region $region`
