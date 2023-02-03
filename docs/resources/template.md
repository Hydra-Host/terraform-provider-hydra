---
page_title: "hydra_virtual_machine - terraform-provider-hydra"
subcategory: ""
description: |-
  Provides a hydra compute resource.
---

# hydra_virtual_machine

Provides a hydra compute resource.

This resource allows you to manage virtual machines on your hydra clusters. When applied,
new virtual machines are created. When destroyed, they are removed.

## Example Usage

```hcl
resource "hydra_virtual_machine" "example" {
  count       = 2
  name        = "virtual-machine-${count.index}"
  description = "VM"
  template_id = data.hydra_template.vm_template.id
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the virtual machine.
* `template_id` - (Required) VM are instantiated from the template ID. Changing this argument triggers a new resource.
* `cpu` - (Optional) Number of processors.
* `memory` - (Optional) Amount of memory in MB.

## Instantiate from a template

You must set `template_id` for the VM to instantiate.
Select the template using tags as described in the template documentation.

## Import

`hydra_virtual_machine` can be imported using its ID:

```shell
terraform import hydra_virtual_machine.example 123
```
