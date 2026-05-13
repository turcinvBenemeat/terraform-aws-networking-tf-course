# AWS Networking Module

A Terraform module that creates a VPC with public and private subnets, an internet gateway, and route table associations for public subnets.

## Resources Created

- **VPC** — with a configurable CIDR block and name tag.
- **Subnets** — any number of public or private subnets across specified availability zones.
- **Internet Gateway** — created only when at least one public subnet is defined.
- **Route Table** — routes `0.0.0.0/0` through the internet gateway for public subnets.
- **Route Table Associations** — links public subnets to the public route table.

## Usage

```hcl
module "vpc" {
  source = "./modules/networking"

  vpc_config = {
    cidr_block = "10.0.0.0/16"
    name       = "my-vpc"
  }

  subnet_config = {
    private_a = {
      cidr_block = "10.0.0.0/24"
      az         = "eu-north-1a"
    }
    public_b = {
      cidr_block = "10.0.1.0/24"
      az         = "eu-north-1b"
      public     = true
    }
  }
}
```

## Inputs

| Name | Type | Description |
|------|------|-------------|
| `vpc_config` | `object({ cidr_block = string, name = string })` | VPC CIDR block and name tag. The CIDR block is validated at plan time. |
| `subnet_config` | `map(object({ cidr_block = string, public = optional(bool, false), az = string }))` | Map of subnets to create. Each key becomes the subnet's name tag. Set `public = true` to route traffic through the internet gateway. The availability zone is validated against the current region. |

## Outputs

| Name | Description |
|------|-------------|
| `vpc_id` | The ID of the created VPC. |
| `public_subnets` | Map of public subnet keys to their `subnet_id` and `availability_zone`. |
| `private_subnets` | Map of private subnet keys to their `subnet_id` and `availability_zone`. |

## License

MIT
