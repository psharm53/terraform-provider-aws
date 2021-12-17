---
name: Terraform failing to capture the resource created
about:  terraform apply resulting in empty result or couldn't fine resource errors #21978 

---

<!---
Please note the following potential times when an issue might be in Terraform core:

* [Configuration Language](https://www.terraform.io/docs/configuration/index.html) or resource ordering issues
* [State](https://www.terraform.io/docs/state/index.html) and [State Backend](https://www.terraform.io/docs/backends/index.html) issues
* [Provisioner](https://www.terraform.io/docs/provisioners/index.html) issues
* [Registry](https://registry.terraform.io/) issues
* Spans resources across multiple providers

If you are running into one of these scenarios, we recommend opening an issue in the [Terraform core repository](https://github.com/hashicorp/terraform/) instead.
--->

<!--- Please keep this note for the community --->

### Community Note

* Please vote on this issue by adding a 👍 [reaction](https://blog.github.com/2016-03-10-add-reactions-to-pull-requests-issues-and-comments/) to the original issue to help the community and maintainers prioritize this request
* Please do not leave "+1" or other comments that do not add relevant new information or questions, they generate extra noise for issue followers and do not help prioritize the request
* If you are interested in working on this issue or have submitted a pull request, please leave a comment

<!--- Thank you for keeping this note for the community --->

### Terraform CLI and Terraform AWS Provider Version

Terraform AWS Provider : 3.69.0
AWS CLI Version : awscli-1.18.147-1.amzn2.0.1.noarch
### Affected Resource(s)

1. Security Group
2. Route Table
3. Route Table Association
4. Route Entry
5. Peering Connection

### Terraform Configuration Files

[Scripts.zip](https://github.com/hashicorp/terraform-provider-aws/files/7732395/Scripts.zip)

### Debug Output

<!---
Please provide a link to a GitHub Gist containing the complete debug output. Please do NOT paste the debug output in the issue; just paste a link to the Gist.

To obtain the debug output, see the [Terraform documentation on debugging](https://www.terraform.io/docs/internals/debugging.html).
--->

### Panic Output

<!--- If Terraform produced a panic, please provide a link to a GitHub Gist containing the output of the `crash.log`. --->

### Expected Behavior

761 resources should have been created without errors
### Actual Behavior

Intermittent errors related to empty result or couldn't fine resource errors
### Steps to Reproduce

<!--- Please list the steps required to reproduce the issue. --->

Pre-requisites:

1. Use Region us-east-1 only
2. IAM role attached to EC2 instance on which terraform will be executed with account administrator access.
3. One VPC , it's CIDR . The VPC should have 2 private route table and one public route table . Keep their route table entires , VPC ID and CIDR handy.
4. Second VPC , it's CIDR . The VPC should have 2 private route table and one public route table . Keep their route table entires , VPC ID and CIDR handy.
5. One S3 bucket with folder names State-Files and FlowLogs in us-east-1


1. Go to Folder Path Scripts/Terraform/Reinvent/StackLdapPeering

2. Edit File for following fields
=> Replace AWS Account ID

   ldap_vpc_access_role => Set to IAM role ARN from pre-requisutes 2.

   ldap_ops_vpc_id = Set to VPC ID from pre-requisutes 3.
   ldap_ops_vpc_cidr = Set to VPC CIDR from pre-requisutes 3.
   <Private route table ID 1 for VPC Alpha> => Replace with one private Route Table ID from pre-requisutes 3.
   <Private route table ID 2 for VPC Alpha> => Replace with second private Route Table ID from pre-requisutes 3.
   Replace with public Route Table ID from pre-requisutes 3.

   ops_vpc_id = Set to VPC ID from pre-requisutes 4.
   ops_vpc_cidr = Set to VPC CIDR from pre-requisutes 4.
   <Private route table ID 1 for VPC Beta> => Replace with one private Route Table ID from pre-requisutes 4.
   <Private route table ID 2 for VPC Beta> => Replace with second private Route Table ID from pre-requisutes 4.

3. Replace the string with S3 bucket from pre-requisutes 5.

4. Execute Commands as follows:
=> export AWS_DEFAULT_REGION="us-east-1"

=> terraform init -reconfigure -input=false -backend-config="encrypt=true" -backend-config="max_retries=100" -backend-config="bucket=<Valid S3 Bucket Name from pre req 5>/State-Files/" -backend-config="region=us-east-1" -backend-config="key=CIOPS-test.tfstate"

=>terraform get

=>terraform plan -input=false -var-file=CIOPS.tfvars
This will show 761 resources to be created

=> terraform apply -auto-approve -input=false -var-file=CIOPS.tfvars

### Important Factoids

<!--- Are there anything atypical about your accounts that we should know? For example: Running in EC2 Classic? --->

### References

<!---
Information about referencing Github Issues: https://help.github.com/articles/basic-writing-and-formatting-syntax/#referencing-issues-and-pull-requests

Are there any other GitHub issues (open or closed) or pull requests that should be linked here? Vendor documentation? For example:
--->

* #0000
