# Terraform notes


```
-- main.tf
  -- servers
    -- main.tf
    -- providers.tf
    -- variables.tf
    -- outputs.tf
```

#### general syntax

- blocks
- arguments
- identifiers
- expressions
- comments

```
<block type> "<block label>" "<block label>" {
	# block body
	<identifier> = <expression> # argument
}
```

## author phase

```
-- main.tf
-- providers.tf
-- variables.tf
-- outputs.tf
```

#### resources
`-- main.tf`

```
resource "resource_type" "resource_name" {
	# resource specific arguments
}
```

#### providers
`-- providers.tf`

```
terraform {
	required providers {
		google = {
			source = "hashicorp/google"
			version = "4.23.0"
		}
	}
}

provider "google" {
	# configuration options
	project = <project_id>
	region = "us-central1"
}
```

#### variables

#### outputs
`-- outputs.tf`
```
output "bucket_URL" {
	value = google_storage_bucket.mybucket.URL
}
```

#### `-- terraform.tfstate`

Terraform module:
  is a set of Terraform configuration files in a single directory

#### commands

- `terraform init` - initialize provider(s) with a plugin
- `terraform plan` - preview resources that will be created
- `terraform apply` - create real infrastructure resources
- `terraform destroy` - destroy resources
- `terraform fmt` - autoformat

#### validator
`terraform validate` - test syntax withoud deploy  
vs
`gcloud beta terraform vet`

## syntax deeply

### block to block reference
accessing a resource attribute from another resource block - `<resource_type>.<resource_name>.<attribute>`  
**!** only when resources are defined within the same root configuration

### meta-arguments

- `count` - multiple instances according to the value
- `for_each` - multiple instances as per set of strings
- `depends_on` - explicit dependency
- `lifecycle` - lifecycle of resource

- `provider` - non default configuration

#### count
```
resource "google_compute_instance" "dev_vm" {
  count = 3
  name = "dev_vm${count.index + 1}"
}
```

#### for_each
```
resource "google_compute_instance" "dev_vm" {
  for_each = toset(["us-central1-a", "asia-east1-b", "europe-west4-a"])
  name = "dev-${each.value}"
  zone = "${each.value}"
}
```

### variables
```
variable "variable_name" {
  type = <variable_type>
  description = "<variable_description>"
  default = "<variable default value>"
  sensitive = true
}
```

`type`: bool, number, string

#### to set variable values at run time:
- .tfvars file - `terraform apply -var-file my_vars.tfvars`
- cli - `terraform apply -var project_id="pid-12345"`
- environment - `TF_VAR_project_id="pid-12345"`
- autoload from files - `terraform.tfvars`, `terraform.tfvars.json`, `auto.tfvars`, `auto.tfvars.json`

**!** .tfvars file overrides the definition in the default argument and environment variable

#### validation
```
variable "mybucket_storageclass" {
  type = string
  description = "set storage class for the bucket"
  validation {
    condition = contains(["standart", "regional"], var.mybucket_storageclass)
    error_message = "Allowed only 'standart' and 'regional'"
  }
}
```

### output values
```
output "picture_rrl" {
  description = "url of the picture uploaded"
  // value = <resource_type>.<resource_name>.<attribute>
  value = google_storage_bucket_object.picture.self_link
}






























```
resource google_compute_network "vpc_network" {
	name = "xxx"
	project =
	auto_create_subnetwork = false
	mtu = 1460
}

resource google_compute_subnetwork "subnetwork-ipv6" {
	name = "ffff"
	ip_cidr_range = "10.0.0.0/22"
	network = google_compute_network.vpc_network.id
	region = "us-west2"
}
```





---
<https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/google_project>

<https://developer.hashicorp.com/terraform/language/functions>

<https://cloud.google.com/docs/terraform/samples>

<https://github.com/xird/geany-hcl-syntax-highlighting>




















