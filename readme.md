# aws_manage_igws

## Description:

Manages AWS Internet Gateways (IGWs) as follows:
- Running the [main.yml](./tasks/main.yml) play will create the IGWs defined in `aws_vpc_igws`.  
- Running the [get_internet_gateway_facts.yml](./tasks/get_internet_gateway_facts.yml) play will return information about the specified IGW.

## Behaviour:

**Feature:** Create EC2 IGW
- **Given** valid AWS credentials
- **Given** an existing VPC name
- **When** the script is executed
- **Then** a VPC IGW is created

## Configuration:

Common variables used by this role:

| Variable | Description | Example |
|---|---|---|
| **aws_region** | AWS region | See [AWS regions](http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region) |
| **aws_access_key** | AWS access key | AKIAIOSFODNN7EXAMPLE |
| **aws_secret_key** | AWS secret key | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY |
| **resource_tags** | List of project tags | see usage |

Variables specific to this role:

| Variable | Description | Example |
|---|---|---|
| **aws_vpc_igws** | List of VPC IGWs to create | see usage |
| **vpc_igw_tag** | The `Name:` tag of the internet gateway to get information about | see usage |

## Usage:

How to invoke the role from a playbook:

```yaml
- name: Create AWS EC2 IGWs
  include_role:
    name: aws_manage_igws
  vars:
    aws_access_key: AKIAIOSFODNN7EXAMPLE
    aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    aws_region: eu-west-2  # London
    resource_tags:
      Name: "{{ resource_name }}"   # Set to upper case aws_vpc_tag
      Owner: "Acme Co."

    aws_vpc_igws:
      - aws_vpc_name: "Test A"
        aws_vpc_region: "{{ aws_region }}"
        aws_vpc_tag: "Test A-IGW"
```

How to get IGW information:

```yaml
- name: Get Internet Gateway facts
  include_role:
    name: aws_manage_igws
    tasks_from: "get_internet_gateway_facts"
  vars:
    vpc_igw_tag: "Test A-IGW"
```

This will return facts in the variable `igw_facts`.  The data returned is as follows:

```yaml
"igw_facts": {
    "changed": false, 
    "failed": false, 
    "internet_gateways": [
        {
            "attachments": [
                {
                    "state": "available", 
                    "vpc_id": "vpc-a48aa2df"
                }
            ], 
            "internet_gateway_id": "igw-12385e3f", 
            "tags": [
                {
                    "key": "Project", 
                    "value": "My Project"
                }, 
                {
                    "key": "Owner", 
                    "value": "Acme Co."
                }, 
                {
                    "key": "Name", 
                    "value": "TEST A-IGW"
                }
            ]
        }
    ]
}
```

