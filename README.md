# Terraform automation for Archehealth infrastructure.

## Prerequisite

1. Install [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli) version `v0.14.11` or later.
2. Install [jq](https://stedolan.github.io/jq/download/).
3. Obtain a Duplo token from the [Duplo documentation](https://docs.duplocloud.com/docs/administrator-tools/access-control/api-tokens).
4. Export the following environment variables in your shell before running the project:

```shell
# Required Vars for duplocloud services
export duplo_host="https://duplo.dev.archehealth.io"
export duplo_token="XXXXXXXXXXXXXXXXXXXXXXXXXXX"

# Required Vars for databricks provider
export DATABRICKS_HOST="https://accounts.cloud.databricks.com"
export DATABRICKS_CLIENT_ID="XXXXXXXXXXXXXXXXXXXXXXXXXXX"
export DATABRICKS_CLIENT_SECRET="XXXXXXXXXXXXXXXXXXXXXXXXXXX"
export DATABRICKS_ACCOUNT_ID="ef5a5518-d66a-4804-b2e0-ae23036c85c6"
```

## Wrapper Scripts

The project includes scripts for managing Terraform infrastructure to set up Archehealth’s infrastructure based on individual tenants.

[scripts/plan.sh](scripts/plan.sh)

[scripts/apply.sh](scripts/apply.sh)

[scripts/destroy.sh](scripts/destroy.sh)

## Terraform Projects

This infrastructure is organized into Terraform sub-projects, each managing different resources such as tenants, AWS services, Databricks workspaces, and Databricks workspace resources.

The repository is organized into the following directories and files:

```
.
├── infra
├── tenant
├── aws-services
├── databricks-ws
├── databricks-ws-resources
│   ├── main.tf                
│   ├── variables.tf           
│   ├── outputs.tf            
│   └── terraform.tfvars.example 

├── config
│   ├── tenant1
│   │   └── aws.services.tfvars.json      # Configuration file for aws services resources
│   │   └── databricks-ws.tfvars.json     # Configuration file for Databricks workspace 
│   │   └── workspace01.tfvars.json       # Configuration file for each workspaces resource 
│   ├── infra
│   │   └── infra.tfvars.json         # Configuration file for infra 
```

- **Project - infra**

  This projects manages duplo infrastructure, Run this project using following command using **infra-name** and **project-name**.

  - Dry-run

    - ```shell
       scripts/plan.sh <infra-name> infra
      ```

  - Actual Deployment

    - ```shell
      scripts/apply.sh <infra-name> infra
      ```

      This will create tenant with provided name in duplo.

  - Destroy created infrastructure

    - ```shell
      scripts/destroy.sh <infra-name> infra
      ```

Update variable name "infra_name" in tenant.tfvars.json file 

- **Project - tenant**

   This projects manages duplo tenant, Run this project using following command using **tenant-name** and **project-name**.

   - Dry-run

    - ```shell
       scripts/plan.sh <tenant-name> tenant
      ```

   - Actual Deployment

    - ```shell
      scripts/apply.sh <tenant-name> tenant
      ```

    This will create tenant with provided name in duplo.

   - Destroy created infrastructure

    - ```shell
      scripts/destroy.sh <tenant-name> tenant
      ```

- **Project - aws-services**

  This project manages services such as S3 and CloudFront.

  - Dry-run

    - ```shell
       scripts/plan.sh <tenant-name> aws-services
      ```

  - Actual Deployment

    - ```shell
      scripts/apply.sh <tenant-name> aws-services
      ```

      This will create s3 bucket, Cloudfront inside duplo tenant which is created as part of admin project.

  - Destroy created infrastructure

    - ```shell
      scripts/destroy.sh <tenant-name> aws-services
      ```


- **Project - databricks-ws**

  This project handles the creation of Databricks account and workspaces.

  - Crete databricks-ws.tfvars.json under tenant directory with following details

  - ```shell
    {
      "group_name": "<GROUP_NAME>",
      "user_emails": [
          "<USERNAME>"
      ],
      "workspaces": [
            "<WORKSPACE_NAME>",
            "<WORKSPACE_NAME>"
      ]
    }
      ```

  - Dry-run

    - ```shell
       scripts/plan.sh <tenant-name>  databricks-ws
      ```

  - Actual Deployment

    - ```shell
      scripts/apply.sh <tenant-name>  databricks-ws
      ```

  - Destroy created infrastructure

    - ```shell
      scripts/destroy.sh <tenant-name>  databricks-ws
      ```

- **Project - databricks-ws-resources**

  This project manages Databricks resources that will be created within the workspace designated as "Project - databricks-ws".

   - Once databricks-ws is deployed, copy workspace name and export it as environment variable shown below
    
    - ```shell
       export DATABRICKS_WORKSPACE=<workspace-name>
       export DATABRICKS_WORKSPACE=archehealth-qa
      ```

    - Create workspace-name.tfvars.json with below content (workspace_url will be available post databricks-ws module deployment)
    
    - ```shell
       {
        "workspace_url" : ""
       }
      ```

  - Dry-run

    - ```shell
       scripts/plan.sh <tenant-name>  databricks-ws-resources
      ```

  - Actual Deployment

    - ```shell
      scripts/apply.sh <tenant-name>  databricks-ws-resources
      ```

  - Destroy created infrastructure

   - ```shell
      scripts/destroy.sh <tenant-name>  databricks-ws-resources
      ```

      
