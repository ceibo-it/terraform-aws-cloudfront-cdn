# terraform-aws-cloudfront-cdn

Terraform Module that implements a CloudFront Distribution (CDN) for a custom origin (e.g. website) and [ships logs to a bucket](https://github.com/ceibo-it/terraform-aws-log-storage). 

If you need to accelerate an S3 bucket, we suggest using [`terraform-aws-cloudfront-s3-cdn`](https://github.com/ceibo-it/terraform-aws-cloudfront-s3-cdn) instead.


## Usage

**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/ceibo-it/terraform-aws-cloudfront-cdn/releases).


Basic usage:

```hcl
module "cdn" {
  source             = "git::https://github.com/ceibo-it/terraform-aws-cloudfront-cdn.git?ref=master"
  namespace          = "ceibo"
  stage              = "prod"
  name               = "app"
  aliases            = ["ceibo.it", "www.ceibo.it"]
  parent_zone_name   = "ceibo.it"
  origin_domain_name = "origin.ceibo.it"
}
```


Complete example of setting up CloudFront Distribution with Cache Behaviors for a WordPress site: [`examples/wordpress`](examples/wordpress/main.tf)


### Generating ACM Certificate

Use the AWS cli to [request new ACM certifiates](http://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request.html) (requires email validation)
```
aws acm request-certificate --domain-name example.com --subject-alternative-names a.example.com b.example.com *.c.example.com
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| acm_certificate_arn | Existing ACM Certificate ARN | string | `` | no |
| aliases | List of aliases. CAUTION! Names MUSTN'T contain trailing `.` | list | `<list>` | no |
| allowed_methods | List of allowed methods (e.g. ` GET, PUT, POST, DELETE, HEAD`) for AWS CloudFront | list | `<list>` | no |
| attributes | Additional attributes (e.g. `policy` or `role`) | list | `<list>` | no |
| cache_behavior | An ordered list of cache behaviors resource for this distribution. List from top to bottom in order of precedence. The topmost cache behavior will have precedence 0. | list | `<list>` | no |
| cached_methods | List of cached methods (e.g. ` GET, PUT, POST, DELETE, HEAD`) | list | `<list>` | no |
| comment | Comment for the origin access identity | string | `Managed by Terraform` | no |
| compress | (Optional) Whether you want CloudFront to automatically compress content for web requests that include Accept-Encoding: gzip in the request header (default: false) | string | `false` | no |
| custom_error_response | (Optional) - List of one or more custom error response element maps | list | `<list>` | no |
| default_root_object | Object that CloudFront return when requests the root URL | string | `index.html` | no |
| default_ttl | Default amount of time (in seconds) that an object is in a CloudFront cache | string | `60` | no |
| delimiter | Delimiter to be used between `name`, `namespace`, `stage`, etc. | string | `-` | no |
| dns_aliases_enabled | Set to false to prevent dns records for aliases from being created | string | `true` | no |
| enabled | Set to false to prevent the module from creating any resources | string | `true` | no |
| forward_cookies | Specifies whether you want CloudFront to forward cookies to the origin. Valid options are all, none or whitelist | string | `none` | no |
| forward_cookies_whitelisted_names | List of forwarded cookie names | list | `<list>` | no |
| forward_headers | Specifies the Headers, if any, that you want CloudFront to vary upon for this cache behavior. Specify `*` to include all headers. | list | `<list>` | no |
| forward_query_string | Forward query strings to the origin that is associated with this cache behavior | string | `false` | no |
| geo_restriction_locations | List of country codes for which  CloudFront either to distribute content (whitelist) or not distribute your content (blacklist) | list | `<list>` | no |
| geo_restriction_type | Method that use to restrict distribution of your content by country: `none`, `whitelist`, or `blacklist` | string | `none` | no |
| is_ipv6_enabled | State of CloudFront IPv6 | string | `true` | no |
| max_ttl | Maximum amount of time (in seconds) that an object is in a CloudFront cache | string | `31536000` | no |
| min_ttl | Minimum amount of time that you want objects to stay in CloudFront caches | string | `0` | no |
| name | Name  (e.g. `bastion` or `db`) | string | - | yes |
| namespace | Namespace (e.g. `cb` or `ceibo`) | string | - | yes |
| origin_domain_name | (Required) - The DNS domain name of your custom origin (e.g. website) | string | `` | no |
| origin_http_port | (Required) - The HTTP port the custom origin listens on | string | `80` | no |
| origin_https_port | (Required) - The HTTPS port the custom origin listens on | string | `443` | no |
| origin_keepalive_timeout | (Optional) The Custom KeepAlive timeout, in seconds. By default, AWS enforces a limit of 60. But you can request an increase. | string | `60` | no |
| origin_path | (Optional) - An optional element that causes CloudFront to request your content from a directory in your Amazon S3 bucket or your custom origin | string | `` | no |
| origin_protocol_policy | (Required) - The origin protocol policy to apply to your origin. One of http-only, https-only, or match-viewer | string | `match-viewer` | no |
| origin_read_timeout | (Optional) The Custom Read timeout, in seconds. By default, AWS enforces a limit of 60. But you can request an increase. | string | `60` | no |
| origin_ssl_protocols | (Required) - The SSL/TLS protocols that you want CloudFront to use when communicating with your origin over HTTPS | list | `<list>` | no |
| parent_zone_id | ID of the hosted zone to contain this record  (or specify `parent_zone_name`) | string | `` | no |
| parent_zone_name | Name of the hosted zone to contain this record (or specify `parent_zone_id`) | string | `` | no |
| price_class | Price class for this distribution: `PriceClass_All`, `PriceClass_200`, `PriceClass_100` | string | `PriceClass_100` | no |
| stage | Stage (e.g. `prod`, `dev`, `staging`) | string | - | yes |
| tags | Additional tags (e.g. `map('BusinessUnit','XYZ')`) | map | `<map>` | no |
| viewer_minimum_protocol_version | (Optional) The minimum version of the SSL protocol that you want CloudFront to use for HTTPS connections. | string | `TLSv1` | no |
| viewer_protocol_policy | allow-all, redirect-to-https | string | `redirect-to-https` | no |
| web_acl_id | (Optional) - Web ACL ID that can be attached to the Cloudfront distribution | string | `` | no |

## Outputs

| Name | Description |
|------|-------------|
| cf_aliases | Extra CNAMEs of AWS CloudFront |
| cf_arn | ARN of AWS CloudFront distribution |
| cf_domain_name | Domain name corresponding to the distribution |
| cf_etag | Current version of the distribution's information |
| cf_hosted_zone_id | CloudFront Route 53 zone ID |
| cf_id | ID of AWS CloudFront distribution |
| cf_origin_access_identity | A shortcut to the full path for the origin access identity to use in CloudFront |
| cf_status | Current status of the distribution |


## Copyright

Copyright © 2020 [Ceibo IT](https://ceibo.it/copyright)


## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.


## NOTICE

terraform-aws-cloudfront-cdn
Copyright 2020 Ceibo


This product includes software developed by
Cloud Posse, LLC (c) (https://cloudposse.com/)
Licensed under Apache License, Version 2.0
Copyright © 2017-2019 Cloud Posse, LLC