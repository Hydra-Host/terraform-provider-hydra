# Contributing to the Hydra Terraform Provider


## Development Environment

### Tools

* [Go](https://go.dev/doc/install)
* [Terraform](https://learn.hashicorp.com/terraform/getting-started/install)

### Download, Build, and Install Local Provider
```shell
# Clone terraform-provider-hydra
git clone git@gitlab.com:hydrahost-developers/oblivion-group/terraform-provider-hydra.git \
  terraform-provider-hydra

# Setup some environment variables for later
export GOOS=linux GOARCH=amd64
export tf_arch=${GOOS}_${GOARCH}
export provider_version=0.0.1

# Build the Provider
cd terraform-provider-hydra
go build
# you should find "terrafor-provider-hydra" in your repo folder

# Create directory under Terraform plugins directory
mkdir -p $HOME/.terraform.d/plugins/hydra.host/hydra/hydra/$provider_version/$tf_arch

# Create a link to the Provider binary
ln -s $(pwd)/terraform-provider-hydra \
  $HOME/.terraform.d/plugins/hydra.host/hydra/hydra/$provider_version/$tf_arch
```

### If you need to check your arch
i.e. see if you need to use `darwin_arm64` or `linux_amd64`.
You can build and run the following go code.
```go
package main
import "fmt"
import "runtime"
func main() { fmt.Printf"(%s_%s",runtime.GOOS,runtime.GOARCH) }
```

### Use The Local Provider

#### `hydra.tf`
```yaml
terraform {
  required_providers {
    hydra = {
      source  = "hydra.host/hydra/hydra"
    }
  }
}

provider "hydra" {
  # ...
}

resource "hydra_virtual_machine" "vm-0" {
  # ...
}
```

The provider will be initialized as `unauthenticated` upon `terraform init`.

```text
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hydra.host/hydra/hydra...
- Installing hydra.host/hydra/hydra v0.0.1...
- Installed hydra.host/hydra/hydra v0.0.1 (unauthenticated)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
Configures your .tf file

```yaml
terraform {
  required_providers {
    hydra = {
      source  = "hydra.host/hydra/hydra"
    }
  }
}

provider "hydra" {
  # Configure with env vars:
  # - HYDRA_USERNAME
  # - HYDRA_PASSWORD
  # - HYDRA_ENDPOINT 
  default_tags {
    tags = {
        test = "test-tag"
    }
  }
}


data "hydra_template" "vm-template" {
  tags = {
    LABELS = "HH-Test"
     #CPU = "2"
     #MEMORY = "1024"
     #DISK_SIZE = "20GB"
  }
}

resource "hydra_virtual_machine" "vm" {
  count = 1
  name = "vm-name-${count.index}"
  template_id = data.hydra_template.vm-template.id
}

```


```yaml
$ terraform plan

#example output 
2023-01-17T14:36:45.763-0700 [INFO]  backend/local: plan operation completed

Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # hydra_virtual_machine.vm[0] will be created
  + resource "hydra_virtual_machine" "vm" {
      + cpu            = (known after apply)
      + default_tags   = (known after apply)
      + gid            = (known after apply)
      + gname          = (known after apply)
      + hard_shutdown  = false
      + id             = (known after apply)
      + ip             = (known after apply)
      + lcmstate       = (known after apply)
      + memory         = (known after apply)
      + name           = "hydra-potato-0"
      + on_disk_change = "swap"
      + pending        = false
      + permissions    = (known after apply)
      + state          = (known after apply)
      + tags_all       = {
          + "test" = "hydra-potato"
        }
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

```
Terraform apply
Take note of Enter Valye. After evaluating the execution plan accept with 'yes' 

```yaml
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # hydra_virtual_machine.vm[0] will be created
  + resource "hydra_virtual_machine" "vm" {
      + cpu            = (known after apply)
      + default_tags   = (known after apply)
      + gid            = (known after apply)
      + gname          = (known after apply)
      + hard_shutdown  = false
      + id             = (known after apply)
      + ip             = (known after apply)
      + lcmstate       = (known after apply)
      + memory         = (known after apply)
      + name           = "hydra-potato-0"
      + on_disk_change = "swap"
      + pending        = false
      + permissions    = (known after apply)
      + state          = (known after apply)
      + tags_all       = {
          + "test" = "hydra-potato"
        }
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
2023-01-17T14:43:58.997-0700 [DEBUG] command: asking for input: "\nDo you want to perform these actions?"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes  

hydra_virtual_machine.vm[0]: Creating...
2023-01-17T14:44:07.146-0700 [INFO]  Starting apply for hydra_virtual_machine.vm[0]
2023-01-17T14:44:07.146-0700 [DEBUG] hydra_virtual_machine.vm[0]: applying the planned Create change
2023-01-17T14:44:07.148-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] setting computed for "default_tags" from ComputedKeys: timestamp=2023-01-17T14:44:07.147-0700
2023-01-17T14:44:07.148-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] setting computed for "template_disk" from ComputedKeys: timestamp=2023-01-17T14:44:07.148-0700
2023-01-17T14:44:07.148-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] setting computed for "vmgroup" from ComputedKeys: timestamp=2023-01-17T14:44:07.148-0700
2023-01-17T14:44:07.148-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] setting computed for "template_nic" from ComputedKeys: timestamp=2023-01-17T14:44:07.148-0700
2023-01-17T14:44:07.148-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] setting computed for "template_tags" from ComputedKeys: timestamp=2023-01-17T14:44:07.148-0700
2023-01-17T14:44:07.227-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 Number of CONTEXT vars: 0: timestamp=2023-01-17T14:44:07.227-0700
2023-01-17T14:44:07.227-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 CONTEXT Map: map[]: timestamp=2023-01-17T14:44:07.227-0700
2023-01-17T14:44:07.227-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 Number of disks: 0: timestamp=2023-01-17T14:44:07.227-0700
2023-01-17T14:44:07.228-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 Number of NICs: 0: timestamp=2023-01-17T14:44:07.228-0700
2023-01-17T14:44:07.228-0700 [INFO]  provider.terraform-provider-hydra: 2023/01/17 14:44:07 [DEBUG] VM template: NAME="vm-name-0"
CONTEXT=[

```
