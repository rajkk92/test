Terraform
Installation
wget https://releases.hashicorp.com/terraform/1.0.1/terraform_1.0.1_linux_amd64.zip
sudo yum install -y unzip
unzip terraform_1.0.1_linux_amd64.zip
sudo cp terraform /usr/bin/terraform
sample-application folder contents
main.tf contains provider and resource group information
variables.tf contains all the variables file which we need to pass
terraform.tfvars contains values for the variables
templates contains codes for installation necessary software to the VM & Rancher cli commands for setting up the cluster
Steps to setup 3 node kubernetes cluster using terraform in azure using sample-application folder
The sample-application contain codes to setup 3 node kubernetes cluster in 3 new VMs, inside new VNet and peer that VNet to Central Management VNet.

terraform init will initialize the provider and validate for syntax error

terraform init
terraform validate
Importing Resource group and existing Central management VNet and Subnet

terraform import azurerm_resource_group.my_rg /subscriptions/f59723b6-b388-448f-8218-6811da7b3a55/resourceGroups/Platform_Dev_20210608
terraform import azurerm_virtual_network.CM-VNET /subscriptions/f59723b6-b388-448f-8218-6811da7b3a55/resourceGroups/Platform_Dev_20210608/providers/Microsoft.Network/virtualNetworks/CM-VNET
terraform import azurerm_subnet.CM-Subnet /subscriptions/f59723b6-b388-448f-8218-6811da7b3a55/resourceGroups/Platform_Dev_20210608/providers/Microsoft.Network/virtualNetworks/CM-VNET/subnets/CM-Subnet
Dryrun using terraform plan by passing the variables; it will display the no:of resource going to deploy

terraform plan -var="node_count=3" -var="resource_prefix=terraformtesting" -var='hostname=["master","worker1","worker2"]' -var="node_address_space=10.11.0.0/16" -var="node_address_prefix=10.11.1.0/24" -var='tags={"Environment":"terraformtest"}'
Actual run using terraform

terraform apply -var="node_count=3" -var="resource_prefix=terraformtesting" -var='hostname=["master","worker1","worker2"]' -var="node_address_space=10.11.0.0/16" -var="node_address_prefix=10.11.1.0/24" -var='tags={"Environment":"terraformtest"}'
terraform tsfstate file
you can view the list of deployed resource from terraform state file

	terraform state list
you can also remove the resource from terraform state file, if you don't want to control it. For eg: I removing the imported resource which I didn't created using this terraform codes

terraform state rm azurerm_resource_group.my_rg
terraform state rm azurerm_virtual_network.CM-VNET
terraform state rm azurerm_subnet.CM-Subnet
terraform destroy
You can delete the deployed resource using terraform destroy command. Note: terraform destroy will remove all the resource mentioned in the state file, So the imported resource also will get delete.

terraform destroy -var="node_count=3" -var="resource_prefix=terraformtesting" -var='hostname=["master","worker1","worker2"]' -var="node_address_space=10.11.0.0/16" -var="node_address_prefix=10.11.1.0/24" -var='tags={"Environment":"terraformtest"}'
