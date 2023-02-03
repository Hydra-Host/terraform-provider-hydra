<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

<a href="https://terraform.io">
    <img src="https://upload.wikimedia.org/wikipedia/commons/0/04/Terraform_Logo.svg" alt="Terraform logo" title="Terraform" height="30" />
</a>
&nbsp;
<a href="https://hydrahost.com/">
    <img src="https://www.hydrahost.com/images/large-og.jpg" alt="Hydrahost logo" title="Hydrawhost" height="30" />
</a>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

# Hydra Terraform Provider

## Usage
We currently support local installation of the provider. This follows a prescriptive path pattern defined by terraform. These instructions should get you to a working state. Future plans include uploading the provider to the terraform registry.

### Install the Provider

1. Get the release for your OS and ARCH: https://github.com/Hydra-Host/terraform-provider-hydra/releases
2. Make note of the OS, ARCH, and VERSION which are used to setup the local path.
3. Create the local directory for the provider binary and move to that directory.
  ```
  mkdir -p ~/.terraform.d/plugins/hydra.host/local/hydra/
  cd ~/.terraform.d/plugins/hydra.host/local/hydra/
  ```

4. Decompressed the file contents.
``` bash 
unzip terraform-provider_"version"_"os"_"arch".zip

```

5. If you extracted the archive somewhere else, find the folder named after the semantic version in the extracted contents (e.g. 0.0.1). Move the folder into the local plugins directory.

``` bash
mv '0.0.1' ~/.terraform.d/plugins/hydra.host/local/hydra/
```

### Create Hydra Compute

#### Configure the Provider
Put your credentials in an .env file and source them.
##### `.env`
``` bash
export HYDRA_USERNAME="name"
export HYDRA_PASSWORD="youroblivionpassword"
export HYDRA_ENDPOINT="https://oblivion.hydra.host:5266/RPC2"
```
``` bash
source .env
```
#### Select a template
Terraform must pull the data for a VM template and then apply that template to your new computer. Select a template using a name from the table or from the templates available in Fireedge.
All images are currently based on Ubuntu 20.04.

| Name             | Description                        |
| ---------------- | ---------------------------------- |
| Ubuntu 20.04     | Random Datacenter                  |
| aws-nca-virt-2   | Cloud, AWS, Silicon Valley         |
| aws-nvir-virt-2  | Cloud, AWS, Virginia               |
| equinix-sv-bmc-1 | Baremetal, Equinix, Silicon Valley |
| equinix-ny-bmc-2 | Baremetal, Equinix, New York       |

#### `hydra.tf` example
``` hcl
terraform {
  required_providers {
    hydra = {
      source  = "hydra.host/local/hydra"
      version = "~> 0.0.1"
    }
  }
}

provider "hydra" {}

data "hydra_template" "compute_template" {
  name = "aws-nvir-virt-2"
}

resource "hydra_virtual_machine" "compute" {
  count       = 1
  name        = "my-vm-${count.index}"
  template_id = data.hydra_template.compute_template.id
  cpu         = 1
  memory      = 512 # in mb
}
```
``` text
$ terraform init
Initializing provider plugins...
- Finding hydra.host/local/hydra versions matching "~> 0.0.1"...
- Installing hydra.host/local/hydra v0.0.1...
- Installed hydra.host/local/hydra v0.0.1 (unauthenticated)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

╷
│ Warning: Incomplete lock file information for providers
│
│ Due to your customized provider installation methods, Terraform was forced to calculate lock
│ file checksums locally for the following providers:
│   - hydra.host/local/hydra
│
│ The current .terraform.lock.hcl file only includes checksums for darwin_arm64, so Terraform
│ running on another platform will fail to install these providers.
│
│ To calculate additional checksums for another platform, run:
│   terraform providers lock -platform=linux_amd64
│ (where linux_amd64 is the platform to generate)
╵

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

``` text
$ terraform plan
data.hydra_template.compute_template: Reading...
data.hydra_template.compute_template: Read complete after 0s [id=0]

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # hydra_virtual_machine.compute[0] will be created
  + resource "hydra_virtual_machine" "compute" {
      + cpu            = (known after apply)
      + default_tags   = (known after apply)
      + gid            = (known after apply)
      + gname          = (known after apply)
      + hard_shutdown  = false
      + id             = (known after apply)
      + ip             = (known after apply)
      + lcmstate       = (known after apply)
      + memory         = (known after apply)
      + name           = "my-vm-0"
      + on_disk_change = "swap"
      + pending        = false
      + permissions    = (known after apply)
      + state          = (known after apply)
      + tags_all       = (known after apply)
      + template_disk  = (known after apply)
      + template_id    = 0
      + template_nic   = (known after apply)
      + template_tags  = (known after apply)
      + timeout        = 20
      + uid            = (known after apply)
      + uname          = (known after apply)
      + vcpu           = (known after apply)

      + vmgroup {
          + role       = (known after apply)
          + vmgroup_id = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take
exactly these actions if you run "terraform apply" now.
```

```
Terraform Apply expected output:

data.hydra_template.compute_template: Reading...
data.hydra_template.compute_template: Read complete after 0s [id=0]

Terraform used the selected providers to generate the following execution plan. Resource actions
are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # hydra_virtual_machine.compute[0] will be created
  + resource "hydra_virtual_machine" "compute" {
      + cpu            = (known after apply)
      + default_tags   = (known after apply)
      + gid            = (known after apply)
      + gname          = (known after apply)
      + hard_shutdown  = false
      + id             = (known after apply)
      + ip             = (known after apply)
      + lcmstate       = (known after apply)
      + memory         = (known after apply)
      + name           = "my-vm-0"
      + on_disk_change = "swap"
      + pending        = false
      + permissions    = (known after apply)
      + state          = (known after apply)
      + tags_all       = (known after apply)
      + template_disk  = (known after apply)
      + template_id    = 0
      + template_nic   = (known after apply)
      + template_tags  = (known after apply)
      + timeout        = 20
      + uid            = (known after apply)
      + uname          = (known after apply)
      + vcpu           = (known after apply)

      + vmgroup {
          + role       = (known after apply)
          + vmgroup_id = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: #review plan and enter 'yes'

hydra_virtual_machine.compute[0]: Creating...
hydra_virtual_machine.compute[0]: Still creating... [10s elapsed]
hydra_virtual_machine.compute[0]: Still creating... [20s elapsed]
hydra_virtual_machine.compute[0]: Still creating... [30s elapsed]
hydra_virtual_machine.compute[0]: Creation complete after 40s [id=47]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.  

```

## Contributing

- [CONTRIBUTING.md](./CONTRIBUTING.md)

## License

- [LICENSE](./LICENSE)

## References
