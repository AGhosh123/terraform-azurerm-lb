# Azure Load Balancer (L4)
[![Changelog](https://img.shields.io/badge/changelog-release-green.svg)](CHANGELOG.md) [![Notice](https://img.shields.io/badge/notice-copyright-yellow.svg)](NOTICE) [![Apache V2 License](https://img.shields.io/badge/license-Apache%20V2-orange.svg)](LICENSE) [![TF Registry](https://img.shields.io/badge/terraform-registry-blue.svg)](https://registry.terraform.io/modules/claranet/lb/azurerm/)

This Terraform module creates an [Azure Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) 
with possible Public IP address and [basic NAT](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-outbound-rules-overview). 

## Version compatibility

| Module version    | Terraform version | AzureRM version |
|-------------------|-------------------|-----------------|
| >= 3.x.x          | 0.12.x            | >= 2.0          |
| >= 2.x.x, < 3.x.x | 0.12.x            | <  2.0          |
| <  2.x.x          | 0.11.x            | <  2.0          |

## Usage

This module is optimized to work with the [Claranet terraform-wrapper](https://github.com/claranet/terraform-wrapper) tool
which set some terraform variables in the environment needed by this module.
More details about variables set by the `terraform-wrapper` available in the [documentation](https://github.com/claranet/terraform-wrapper#environment).

You can use this module by including it this way:

```hcl
module "azure-region" {
  source  = "claranet/regions/azurerm"
  version = "x.x.x"

  azure_region = var.azure_region
}

module "rg" {
  source  = "claranet/rg/azurerm"
  version = "x.x.x"

  location    = module.azure-region.location
  client_name = var.client_name
  environment = var.environment
  stack       = var.stack
}

module "lb" {
  source  = "claranet/lb/azurerm"
  version = "x.x.x"

  client_name         = var.client_name
  environment         = var.environment
  location            = module.azure-region.location
  location_short      = module.azure-region.location_short
  resource_group_name = module.rg.resource_group_name
  stack               = var.stack

  allocate_public_ip = true
  enable_nat         = true
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| allocate\_public\_ip | True to allocate a Public IP to the Load Balancer. | `bool` | `false` | no |
| client\_name | Client name/account used in naming | `string` | n/a | yes |
| enable\_nat | True to enable NAT through Load Balancer outbound rules. | `bool` | `false` | no |
| environment | Project environment | `string` | n/a | yes |
| extra\_tags | Extra tags to add on all resources. | `map(string)` | `{}` | no |
| ip\_custom\_name | Name of the Public IP address, generated if not set. | `string` | `""` | no |
| ip\_extra\_tags | Extra tags to add to the Public IP address. | `map(string)` | `{}` | no |
| lb\_custom\_name | Name of the Load Balancer, generated if not set. | `string` | `""` | no |
| lb\_extra\_tags | Extra tags to add to the Load Balancer. | `map(string)` | `{}` | no |
| lb\_frontend\_ip\_configurations | `frontend_ip_configuration` blocks as documented here: https://www.terraform.io/docs/providers/azurerm/r/lb.html#frontend_ip_configuration | `map(any)` | `{}` | no |
| location | Azure location. | `string` | n/a | yes |
| location\_short | Short string for Azure location. | `string` | n/a | yes |
| name\_prefix | Optional prefix for the generated name | `string` | `""` | no |
| nat\_allocated\_outbound\_ports | Number of allocated outbound ports for NAT. | `number` | `1024` | no |
| nat\_protocol | Transport protocol to use for NAT. | `string` | `"All"` | no |
| public\_ip\_allocation\_method | Allocation method for the Public IP address, can be `Static`, `Dynamic`. | `string` | `"Static"` | no |
| public\_ip\_sku | SKU name for the Public IP address, can be `Basic` or `Standard`. | `string` | `"Standard"` | no |
| resource\_group\_name | Resource group name | `string` | n/a | yes |
| sku\_name | The Name of the SKU used for this Load Balancer. Possible values are "Basic" and "Standard". | `string` | `"Standard"` | no |
| stack | Project stack name | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| backend\_address\_pool\_id | Id of the associated default backend address pool |
| backend\_address\_pool\_ip\_configurations | IP configurations of the associated default backend address pool |
| backend\_address\_pool\_load\_balancing\_rules | Load balancing rules of the associated default backend address pool |
| backend\_address\_pool\_name | Name of the associated default backend address pool |
| lb\_id | Id of the Load Balancer |
| lb\_name | Name of the Load Balancer |
| lb\_private\_ip\_address | Private IP address of the Load Balancer |
| lb\_private\_ip\_addresses | Private IP addresses of the Load Balancer |
| outbound\_rule\_allocated\_outbound\_ports | Number of allocated oubound ports of the default outbound rule if any |
| outbound\_rule\_id | Id of the default outbound rule if any |
| outbound\_rule\_name | Name of the default outbound rule if any |
| pubip\_domain\_name\_label | Domain name label of the Public IP address if any |
| pubip\_fqdn | FQDN of the Public IP address if any |
| pubip\_id | Id of the Public IP address if any |
| pubip\_ip\_address | IP address of the Public IP address if any |

## Related documentation

Terraform resource documentation: [terraform.io/docs/providers/azurerm/r/lb.html](https://www.terraform.io/docs/providers/azurerm/r/lb.html)

Microsoft Azure documentation: [docs.microsoft.com/en-us/azure/load-balancer/](https://docs.microsoft.com/en-us/azure/load-balancer/)
