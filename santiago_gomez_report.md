# Azure Functions with Terraform: Santiago Gomez Report

## Activity Overview
For this activity, the goal was to deploy an Azure Function using Infrastructure as Code (IaC) principles with Terraform. The repository provided a clear structure consisting of a `main.tf` file to declare resources, `variables.tf` for inputs, and `outputs.tf` for the resulting endpoint. Additionally, there was a sample Node.js HTTP trigger function located in the `example/index.js` directory.

## What I Learned
Through this activity, I gained hands-on experience with several key concepts:
1. **Infrastructure as Code (IaC):** I learned how to represent cloud infrastructure using declarative configuration files. This approach makes deployments repeatable, trackable, and version-controlled.
2. **Azure Resource Hierarchy:** I learned that an Azure Function does not exist in isolation. To deploy it, I had to configure an `azurerm_resource_group`, an `azurerm_storage_account` (for internal function state and file storage), an `azurerm_service_plan` (defining the compute resources, in this case, the Consumption plan `Y1`), and finally the `azurerm_windows_function_app` and `azurerm_function_app_function` themselves.
3. **Variable Management:** Following the best practices outlined in the README, I avoided hardcoding values by using a `dev.tfvars` file to inject the `name_function` and `location` variables dynamically.
4. **Terraform Workflow:** I practiced the standard Terraform workflow: `terraform init` to download the `azurerm` provider, followed by `terraform apply` to provision the resources.

## Difficulties Encountered
While the Terraform code was well-structured and conceptually correct, I encountered a significant deployment blocker related to **Azure Governance and Policies**. 

During the `terraform apply` phase, the creation of the Storage Account and the App Service Plan failed with a `403 Forbidden` error:
> `RequestDisallowedByAzure: Resource 'robledoazfunc1234' was disallowed by Azure: This policy maintains a set of best available regions where your subscription can deploy resources.`

**Troubleshooting Steps Taken:**
- I initially attempted to deploy to `East US`. When it failed, I assumed it might be a regional outage or quota issue.
- I modified the `dev.tfvars` file to change the region to `West Europe` (the default in the `variables.tf` file) and subsequently tried `Central US`. 
- Every attempt yielded the same policy restriction error. 

**Root Cause:**
Since I am using an **Azure for Students** subscription under a university tenant, there are strict organizational policies assigned to the subscription. These policies restrict the regions where compute and storage resources can be deployed to optimize costs and availability for free-tier student accounts. 

**Conclusion:**
Although the infrastructure was not successfully provisioned on the cloud side due to subscription constraints, the IaC logic, Terraform plan, and syntax were perfectly valid. This difficulty served as a valuable real-world lesson on how Cloud Administrators use Azure Policies to enforce compliance, restrict deployments, and manage quotas across subscriptions.
