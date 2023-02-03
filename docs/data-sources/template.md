---
page_title: "hydra_template Data Source - terraform-provider-hydra"
subcategory: ""
description: |-
  Get a VM template.
---

# hydra_template

Use this data source to retrieve a template required by hydra_virtual_machine.

## Example Usage

```hcl
data "hydra_template" "vm_template" {
  name = "aws-nvir-virt-2"
}
```

## Argument Reference

* `name` - (Optional) Template name.
* `tags` - (Optional) Template tags (Key = Value).

## Attribute Reference

The following attributes are exported:

* `id` - ID of the template.
* `name` - Name of the template.
* `cpu` - Amount of CPU shares assigned to the VM.
* `memory` - Amount of RAM assigned to the VM in MB.
* `vmgroup` - VM group parameters
* `tags` - Tags of the template (Key = Value).
