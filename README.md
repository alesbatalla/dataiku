# dataiku
Dataiku test

### Provisioning

- Review /azure_templates dir to create a VM with disk.
``` bash
az deployment group create -g sans1weursgcldk8stech001 --name dataiku_dg --template-file azure-template/template.json --parameters @azure-template/parameters.json
az vm extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --vm-name dataiku1 \
  --resource-group sans1weursgcldk8stech001 \
  --settings '{"commandToExecute":"sudo yum install -y ntp git java-1.8.0-openjdk python3 libgfortran"}'


az vm delete  --name dataiku1   --resource-group  sans1weursgcldk8stech001  -y
az disk delete --name dataiku1_OSDisk_0 --resource-group sans1weursgcldk8stech001  -y
az network nic delete --name dataiku_ni_1 --resource-group sans1weursgcldk8stech001
az network public-ip delete -g sans1weursgcldk8stech001 -n dataiku-ip_1

```

### Config and install Default Login: admin Password: admin
- Review /azure_templates dir to create a VM with disk.
``` bash
sestatus
sudo vi /etc/selinux/config
	SELINUX=disabled
reboot

curl https://releases.rancher.com/install-docker/18.09.2.sh | sh
sudo usermod -aG docker ${USER}
# reconnect

wget https://cdn.downloads.dataiku.com/public/dss/7.0.2/dataiku-dss-7.0.2.tar.gz
tar xzf dataiku-dss-7.0.2.tar.gz

sudo rm -rf /opt/dataiku
sudo mkdir -p /opt/dataiku
sudo chown rancher:rancher /opt/dataiku

sudo -i "/home/rancher/dataiku-dss-7.0.2/scripts/install/install-deps.sh"
dataiku-dss-7.0.2/installer.sh -d /opt/dataiku -p 8080

/opt/dataiku/bin/dss start

#Create base image
#dssadmin build-container-exec-base-image
```


### Install hadoop, hive, spark

-Install cdh gateways.

``` bash
hadoop version
hdfs dfs -ls /
beeline -u jdbc:hive2://cdsw:10000/default -u admin -p admin
dss stop
dssadmin install-hadoop-integration
dssadmin install-spark-integration
dss start
```


### Install AKS cluster

-Install AKS unmanaged cluster.

``` bash
az acr create --resource-group sans1weursgcldk8stech001 --name akssanregistry --sku Basic
az aks create --resource-group sans1weursgcldk8stech001 --name akssanunmanaged --node-count 1 --enable-addons monitoring --generate-ssh-keys
# login registry az acr login --name akssanregistry

az acr delete -n akssanregistry
az aks delete --name akssanunmanaged --resource-group sans1weursgcldk8stech001 --no-wait
```




