---
page_title: "Hydra Provider"
subcategory: ""
description: |-
  Terraform Provider for Hydra
---

Use the hydra provider to interact with hydra compute resources.
It needs to be configured with proper credentials.

## Example Usage

```hcl
terraform {
  required_providers {
    hydra = {
      source  = "hydra.host/hydra/hydra"
      version = "0.0.1"
    }
  }
}

provider "hydra" {
  endpoint      = "https://example.com:2633/RPC2"
}

## Authentication and Configuration

The configuration of the hydra provider can be set by the `provider` block attributes or by the environment variables.

### Provider configuration

* `endpoint` - (Required) The URL of hydra XML-RPC Endpoint API (for example, `http://example.com:2633/RPC2`).
* `username` - (Required) The hydra username.
* `password` - (Required) The hydra password matching the username.
* `default_tags` - (Optional) Apply default custom tags to created resources: group, image, security_group, template, vm_group, user, virtual_machine, virtual_network, virtual_router, virtual_router_instance. Theses tags could be overriden in the tag section of the resource. See [Default Tags parameters](#default-tags-parameters) below for details.

#### Default Tags parameters

`default_tags` supports the following arguments:

* `tags` - (Optional) Map of tags.

#### Example

```hcl
provider "hydra" {
  endpoint      = "https://example.com:2633/RPC2"
  username      = "me"
  password      = "p@s5w0rD"
  insecure      = true

  default_tags {
    tags = {
      environment = "default"
    }
  }
}

```bash
terraform init
terraform plan
```

!> **Warning:** Hard-coded credentials are not recommended in any Terraform configuration file and should not be commited in a public repository.

### Environment variables

The provider can also read the following environment variables if no value is set in the the `provider` block attributes:

* `HYDRA_ENDPOINT`
* `HYDRA_USERNAME`
* `HYDRA_PASSWORD`

#### Example

```bash
export HYDRA_ENDPOINT="https://example.com:2633/RPC2"
export HYDRA_USERNAME="me"
export HYDRA_PASSWORD="p@s5w0rD"
```

```hcl
provider "hydra" {
    default_tags {
    tags = {
        environment = local.env
    }
  }
}

data "hydra_template" "vm_template" {
  tags = {
    CPU = 2
    MEMORY = 768
  }
}

resource "hydra_virtual_machine" "vm" {
  count = 2
  name = "vm-${count.index}"
  template_id = data.hydra_template.vm_template.id
}

```
