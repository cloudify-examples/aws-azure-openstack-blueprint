[![Build Status](https://circleci.com/gh/cloudify-examples/aws-azure-openstack-blueprint.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/cloudify-examples/aws-azure-openstack-blueprint)

# composition-blueprint

This blueprint creates a multi-subnet network with a single instance.

The `blueprint.yaml` file in the root directory can be used to create such a network in AWS, Azure, and Openstack each:

Given an inputs file with the required properties, you can run this command:

`cfy install blueprint.yaml --task-retry-interval=10 --task-retries=25  -i inputs.yaml`

## AWS

To create on AWS only, run this command:

`cfy install aws/blueprint.yaml --task-retry-interval=10 --task-retries=25  -i inputs.yaml`

## Azure

To create on Azure only, run this command:

`cfy install azure/blueprint.yaml --task-retry-interval=10 --task-retries=25  -i inputs.yaml`


## Openstack

To create on Openstack only, run this command:

`cfy install openstack/blueprint.yaml --task-retry-interval=10 --task-retries=25  -i inputs.yaml`


## "Hybrid" or Mixed Environments

You can also combine specific parts of environments. To do this, create your own `blueprint.yaml` file.

Here is an example content of such a mixed environment, including a public network and virtual machine in each Openstack and AWS. The AWS VM is imported directly from this repository, while the Openstack VM is specified here:

```yaml
tosca_definitions_version: cloudify_dsl_1_3

imports:

  # AWS
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/imports.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/network/vpc.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/network/dhcp-options-set.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/network/public-subnet/subnet.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/network/public-subnet/route-table.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/network/public-subnet/internet-gateway.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/compute/elastic-ip.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/compute/key.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/compute/security-group.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/aws/compute/virtual-machine.yaml

  # Openstack
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/imports.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/external-network.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/open-group.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/public-network/router.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/public-network/floating-ip.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/public-network/network.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/public-network/subnet.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/network/public-network/port.yaml
  - https://raw.githubusercontent.com/cloudify-examples/composition-blueprint/master/openstack/compute/key.yaml

inputs:

  openstack_image_id:
    default: ''

  openstack_flavor_id:
    default: ''

node_templates:

  modified_example_openstack_virtual_machine:
    type: cloudify.openstack.nodes.Server
    properties:
      openstack_config: { get_input: openstack_configuration }
      install_agent: false
      server:
        image: { get_input: openstack_image_id }
        flavor: { get_input: openstack_flavor_id }
      management_network_name: { get_property: [ example_openstack_network, resource_id ] }
    relationships:
      - target: example_openstack_key
        type: cloudify.openstack.server_connected_to_keypair
      - target: example_openstack_network_port
        type: cloudify.openstack.server_connected_to_port
```