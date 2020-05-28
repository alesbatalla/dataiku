# dataiku
Dataiku test

### Configuration and installation

- Review /azure_templates dir to create a VM with disk.
``` bash
az deployment group create -g sans1weursgcldk8stech001 --name dataiku_dg --template-file azure-template/template.json --parameters @azure-template/parameters.json
az vm extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --vm-name dataiku1 \
  --resource-group sans1weursgcldk8stech001 \
  --settings '{"commandToExecute":"sudo yum install -y ntp"}'


az vm delete  --name dataiku1   --resource-group  sans1weursgcldk8stech001  -y
az disk delete --name dataiku1_OSDisk_0 --resource-group sans1weursgcldk8stech001  -y
az network nic delete --name dataiku_ni_1 --resource-group sans1weursgcldk8stech001
az network public-ip delete -g sans1weursgcldk8stech001 -n dataiku-ip_1
```
