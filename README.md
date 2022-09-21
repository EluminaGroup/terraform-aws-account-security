# terraform-aws-account-security

This terraform module creates IAM roles for federated users to assume from an IdP account.

Deploy this module to every AWS account, except _IdP_ and _master_.

You will need an AWS Organization created in the _master_ account and an IdP account with federated login.

Use `idp_admin_trust_account_ids` and `idp_admin_trust_names` to allow access from external accounts. Enter a list of IDs of IDP accounts that will be able to assume to this account.

See:

* Create an organization with [terraform-aws-organization](https://github.com/EluminaGroup/terraform-aws-organization)
* Create accounts with [terraform-aws-account](https://github.com/EluminaGroup/terraform-aws-account)
* Deploy IdP IAM roles (for gsuite) with [terraform-aws-idp-gsuite](https://github.com/EluminaGroup/terraform-aws-idp-gsuite)

This modules creates the following resources:

 - Identity and Access Management2 roles:
   - `idp-admin` with full admin permissions
   - `idp-read-only` with read-only permissions

In addition you have the option to:

 - Creates a client-admin/client-read-only role for external IDPs
 - Create admin and read-only roles trusting IDP account
 - Create IAM role to assume from management account for CI deployments
 - Create IAM instance profile and user for use with CI workers deployed to the account
 - Set Maximum CLI/API session duration. The default value is 43200

## Usage

```hcl
module "my_account_roles" {
  source                = "git::https://github.com/EluminaGroup/terraform-aws-account-roles.git?ref=1.0.0"
  org_name              = "my_organization"
  account_name          = "my_account"
  idp_account_id        = "000000000000"

  idp_admin_trust_account_ids = ["1234567890123"] # optional
  idp_admin_trust_names       = ["elumina"]                        # optional
}
```

<!--- BEGIN_TF_DOCS --->

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.12.0 |

## Providers

| Name | Version |
|------|---------|
| aws | n/a |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| account\_name | Account name (slug) | `any` | n/a | yes |
| ci\_account\_id | Account ID of MGMT account for use with IAM CI role. Required when create\_ci\_iam\_role=true | `string` | `""` | no |
| create\_ci\_profile | Create IAM instance profile and user for use with CI workers deployed to the account | `bool` | `false` | no |
| create\_ci\_role | Create IAM role to assume from MGMT account for CI deployments | `bool` | `true` | no |
| create\_idp\_trusted\_roles | Create admin and read-only roles trusting IDP account | `bool` | `true` | no |
| extra\_roles | A list of extra roles to create in this account | `list` | `[]` | no |
| extra\_roles\_policy | A map of { <role\_name> = <json policy> } to create policies to extra roles in this account (role must be declared at extra\_roles first) | `map` | `{}` | no |
| extra\_roles\_policy\_arn | A map of { <role\_name> = <policy arn> } to attach policies to extra roles in this account (role must be declared at extra\_roles first) | `map` | `{}` | no |
| idp\_account\_id | Account ID of IDP account (needs to be set when is\_idp\_account=true) | `string` | `""` | no |
| idp\_external\_trust\_account\_ids | List of account IDs to trust as external IDPs (leave empty to disable external access) | `list(string)` | `[]` | no |
| idp\_external\_trust\_client\_role | Creates a client-admin/client-read-only role for external IDPs | `bool` | `true` | no |
| org\_name | Name for this organization (slug) | `any` | n/a | yes |
| role\_max\_session\_duration | Maximum CLI/API session duration | `string` | `"43200"` | no |
| ssm\_account\_ids | List of account IDs to save in SSM | `list(string)` | `[]` | no |
| ssm\_account\_names | List of account names (slugs) to save in SSM, must match ssm\_account\_ids | `list(string)` | `[]` | no |

## Outputs

| Name | Description |
|------|-------------|
| iam\_role\_admin\_arn | ARN for admin IAM role |
| iam\_role\_read\_only\_arn | ARN for read-only IAM role |

<!--- END_TF_DOCS --->

## Authors

Module managed by [Elumina Group](https://github.com/EluminaGroup).

## License

Apache 2 Licensed. See [LICENSE](https://github.com/EluminaGroup/terraform-aws-account-security/blob/master/LICENSE) for full details.
