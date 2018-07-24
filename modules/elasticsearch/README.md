# AWS Elasticsearch module

This modules creates an Elasticsearch cluster in a domain, with redirection
service to allow easy access to Kibana web interface (requires DNS CNAME alias
to be set up).

## Registered `consul` service name

The registered `consul` service name is `elasticsearch`, and the default port
used is `443`.

The actual VPC service and port are registered in `consul`. Any other services
that require Elasticsearch service should always use the actual VPC service
name, since the service is hosted under SSL and the SSL certificate to accept is
registered under the VPC name (and not the `consul` service name).

## Example Terraform configuration

```hcl
module "es" {
  security_group_vpc_id = "..."
  security_group_tags   = "..."
  route53_zone_internal = "..."
  es_access_cidr_block  = []
  es_vpc_subnet_ids     = []

  redirect_alias_name  = ""
  redirect_job_region  = "ap-southeast-1"
  redirect_job_vpc_azs = []
}
```

## Inputs

| Name                    | Description                                                                                                                                                                                 |  Type  |          Default           | Required |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----: | :------------------------: | :------: |
| es_access_cidr_block    | Elasticsearch access CIDR block to allow access                                                                                                                                             |  list  |             -              |   yes    |
| es_additional_tags      | Additional tags to apply on Elasticsearch                                                                                                                                                   | string |          `<map>`           |    no    |
| es_consul_service       | Name to register in consul to identify Elasticsearch service                                                                                                                                | string |      `elasticsearch`       |    no    |
| es_default_access       | Rest API / Web UI access                                                                                                                                                                    |  map   |          `<map>`           |    no    |
| es_domain_name          | Elasticsearch domain name                                                                                                                                                                   | string |      `tf-l-cloud-es`       |    no    |
| es_ebs_volume_size      | Volume capacity for attached EBS in GB for each node                                                                                                                                        | string |           `200`            |    no    |
| es_ebs_volume_type      | Storage type of EBS volumes, if used (default gp2)                                                                                                                                          | string |           `gp2`            |    no    |
| es_encrypt_at_rest      | Encrypts the data stored by Elasticsearch at rest                                                                                                                                           | string |          `false`           |    no    |
| es_instance_count       | Number of nodes to be deployed in Elasticsearch                                                                                                                                             | string |            `6`             |    no    |
| es_instance_type        | Elasticsearch instance type for non-master node                                                                                                                                             | string | `r4.2xlarge.elasticsearch` |    no    |
| es_kms_key_id           | KMS Key ID for encryption at rest. Not used if left empty.                                                                                                                                  | string |          `` | no           |
| es_management_iam_roles | List of IAM role ARNs from which to permit management traffic (default ['*']). Note that a client must match both the IP address and the IAM role patterns in order to be permitted access. |  list  |          `<list>`          |    no    |
| es_master_type          | Elasticsearch instance type for dedicated master node                                                                                                                                       | string | `r4.2xlarge.elasticsearch` |    no    |
| es_snapshot_start_hour  | Hour at which automated snapshots are taken, in UTC (default 0)                                                                                                                             | string |            `19`            |    no    |
| es_version              | Elasticsearch version to deploy                                                                                                                                                             | string |           `5.5`            |    no    |
| es_vpc_subnet_ids       | Subnet IDs for Elasticsearch cluster                                                                                                                                                        |  list  |             -              |   yes    |
| es_zone_awareness       | Enable zone awareness for Elasticsearch cluster                                                                                                                                             | string |           `true`           |    no    |
| redirect_alias_name     | Alias name of the internal redirect to kibana                                                                                                                                               | string |             -              |   yes    |
| redirect_job_name       | Name of the job to redirect users to kibana                                                                                                                                                 | string |     `kibana-redirect`      |    no    |
| redirect_job_region     | AWS region to run the redirect job                                                                                                                                                          | string |             -              |   yes    |
| redirect_job_vpc_azs    | List of VPC AZs to run the redirect job in                                                                                                                                                  |  list  |             -              |   yes    |
| redirect_nginx_version  | Image tag of Nginx to use                                                                                                                                                                   | string |       `1.14-alpine`        |    no    |
| redirect_subdomain      | Subdomain for internal redirect to kibana                                                                                                                                                   | string |          `kibana`          |    no    |
| route53_zone_internal   | AWS Route53 Zone Internal Domain for Elasticsearch cluster                                                                                                                                  | string |             -              |   yes    |
| security_group_name     | Name of security group, leaving this empty generates a group name                                                                                                                           | string |        `l-cloud-es`        |    no    |
| security_group_tags     | Tags to apply on the security group                                                                                                                                                         | string |             -              |   yes    |
| security_group_vpc_id   | VPC ID to apply on the security group                                                                                                                                                       | string |             -              |   yes    |

## Outputs

| Name              | Description                                                                     |
| ----------------- | ------------------------------------------------------------------------------- |
| arn               | ARN of the created Elasticsearch domain                                         |
| domain_id         | Unique identifier for the domain                                                |
| elasticsearch_url | Elasticsearch URL                                                               |
| endpoint          | Domain-specific endpoint used to submit index, search, and data upload requests |
| kibana_url        | Kibana URL                                                                      |
| port              | Elasticsearch service port                                                      |