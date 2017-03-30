[![Build Status](https://circleci.com/gh/cloudify-examples/aws-azure-openstack-blueprint.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/cloudify-examples/aws-azure-openstack-blueprint)

# aws-azure-openstack-blueprint

This blueprint is actually many blueprints that may be used separately or together.

The most common scenario is that one of these blueprint files will be installed:
* aws/blueprint.yaml
* azure/blueprint.yaml
* openstack/blueprint.yaml


### Install AWS Example Environment

First update the *inputs/aws.yaml.example* file with your credentials and override any inputs that you wish.

To install:
> $ cfy install aws/blueprint.yaml -i inputs/aws.yaml.example --task-retries=15 --task-retry-interval=15```

Initializing local profile ...
Initialization completed successfully
Initializing blueprint...
Initialized blueprint.yaml
If you make changes to the blueprint, run `cfy init blueprint.yaml` again to apply them
2019-12-31 00:00:00.000  CFY <local> Starting 'install' workflow execution


When the install workflow finishes, you can run the following to see useful data of all of the resources that were created:

> $ cfy deployments outputs

{
  ...
  "example_aws_private_subnet": {
    "example_aws_private_subnet": "subnet-20e90f69"
  },
  ...
  ...
  "example_aws_public_subnet": {
    "example_aws_public_subnet": "subnet-b7ed0bfe"
  },
  ...
  ...
  "example_aws_vpc": {
    "example_aws_vpc": "vpc-644d5400"
  }
  ...
}

To get the runtime properties of a specific node:

> $ cfy node-instances example_aws_elastic_ip

[
 ...
     "id": "example_aws_elastic_ip_q0qu0b",
     "name": "example_aws_elastic_ip",
 ...
     "runtime_properties": {
 ...
       "aws_resource_id": "52.208.38.8",
 ...
]


### Azure

To install: ```cfy install azure/blueprint.yaml```

### Openstack

To install: ```cfy install openstack/blueprint.yaml```

