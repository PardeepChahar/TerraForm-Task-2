# TerraForm-Task-2

1. Create a key pair
2. 
3. Create a security group
4. 
5. Launch an instance using the above created key pair and security group.
6. 
7. Create an EBS volume of 1 GB.
8. 
9. The final step is to attach the above created EBS volume to the  instance you created in the previous steps.
10. 
Write a terraform code for all the above steps.

# Create new key pair
```resource "aws_key_pair" "new-key" {
  key_name   = "my-new-key"
  public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCz5LhsXvvkBTAfPfFnMdleZ/fLfFHIj8sFuZPU7+TLWnY0wSFLtPBgluyxvGeBZ0k4hZJgU7iKGIP3YqciBfUBw/iGxP7FB2e6AYksvlC8KqS/MALfIgIj6c9wy1Rb/FbkiFnR1bOrObMHujDr3eiDDTrqQ+kHuAwt6ykK8tK8kP0URj1RfHEpy21dCjNxlj4vBNmrtJY9W/l3R+SJVkuhxS3veNTpRAhfaREa7WYQaG8SN/XzWvkk2DO25Zwls8n4PgL/6yStVvg2etDIy5+tMOZT/zl7s+SZGPjNOQB5RQCXv9XVUMZL8GObYJDheR/cZNvdrYcGT8kPR7kbAoQp pardeep@DESKTOP-SC6L3RT"
}
```
# Create secrity group in default vpc
```resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow ssh inbound traffic"
  vpc_id      = "vpc-70c4241b"

  ingress {
    description      = "ssh from VPC"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "allow_ssh"
  }
}
```
# Create ec2 instance
```resource "aws_instance" "new-ec2" {
  ami           = "ami-00bf4ae5a7909786c"
  instance_type = "t2.micro"

  key_name = aws_key_pair.new-key.key_name
  security_groups  = [aws_security_group.allow_ssh.name]
}```

# Create ebs of 1gb
```resource "aws_ebs_volume" "ebs1"{
 availability_zone = aws_instance.new-ec2.availability_zone 
 size = 1
 tags = {
  Name = "Extra_EBS"
 }
}
```
# Attach created ebs with ec2
```resource "aws_volume_attachment" "attach_ebs_1"{
device_name = "/dev/sdh"
volume_id = aws_ebs_volume.ebs1.id
instance_id =aws_instance.new-ec2.id
}
```
