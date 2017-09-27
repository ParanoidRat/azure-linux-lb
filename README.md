# Load-balanced Linux Cluster
This repository contains Azure templates to deploy a load-balanced Linux-based
generic cluster in Microsoft Azure Recourses Manager (ARM). It also has
[cisco-csr1000v.json](cisco-csr1000v.json) template for deployment of Cisco
CSR-1000v virtual router to build VPN tunnels to the environment.

## Getting Started
```
/usr/bin/az group deployment create \
  --resource-group "RG-NAME" \
  --name "DEPLOYMENT-NAME" \
  --template-uri "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master/main.json" \
  --parameters "@/path/to/params.json"
```

## Parameters: Main template
```json
"templateBaseUrl": {"value": "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master"},
"vmNamespace": { "value": "fustercluck" },
"vmCount": { "value": 2 },

"vmConfig": { "value": {
  "computeApiVersion": "2017-03-30",

  "vmSize": "Standard_DS2",

  "osManagedDiskType": "Premium_LRS",
  "osManagedDiskCache": "ReadWrite",

  "dataManagedDiskEnabledOrDisabled": "Enabled",
  "dataManagedDiskType": "Premium_LRS",
  "dataManagedDiskCount": 4,
  "dataManagedDiskSizeGb": 64,
  "dataManagedDiskCache": "None",

  "tags": {
    "Environment": "Development"
  },

  "imagePublisher": "Canonical",
  "imageOffer": "UbuntuServer",
  "imageSKU": "16.04-LTS",

  "adminUsername": "<USER-NAME>",
  "sshPublicKey": "ssh-rsa <SSH-PUB-KEY>"
} },

"netConfig": { "value": {
  "networkApiVersion": "2017-06-01",

  "publicIPAddressType": "Static",

  "vNetName": "<VNET-NAME>",
  "vNetAddressPrefix": "10.0.0.0/16",

  "vNetSubnets": [
    {
      "name": "subnet-ext",
      "prefix": "10.0.1.0/24",
    },
    {
      "name": "subnet-int",
      "prefix": "10.0.2.0/24",
    }
  ],

  "networkSecurityRules": [
    {
      "ruleName": "SSH-HOST01",
      "ruleDescription": "Allows inbound SSH from specific host",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "22",
      "ruleSourceAddressPrefix": "8.8.8.8/32",
      "ruleDestinationAddressPrefix": "*",
    },
    {
      "ruleName": "SSH-HOST02",
      "ruleDescription": "Allows inbound SSH from specific host",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "22",
      "ruleSourceAddressPrefix": "4.4.4.4/32",
      "ruleDestinationAddressPrefix": "*",
    },
  ],
} }
```

## Parameters: Cisco CSR 1000v template
```json
"vmNamespace": { "value": "vpn" },

"vmConfig": { "value": {
  "computeApiVersion": "2017-03-30",

  "vmName": "cisco-csr",

  "vmSize": "Standard_DS2",

  "osManagedDiskType": "Premium_LRS",
  "osManagedDiskCache": "ReadWrite",

  "tags": {
    "Environment": "Development"
  },

  "imagePublisher": "cisco",
  "imageOffer": "cisco-csr-1000v",
  "imageSKU": "16_6",

  "adminUsername": "<USER-NAME>",
  "sshPublicKey": "ssh-rsa <SSH-PUB-KEY>"
} },

"netConfig": { "value": {
  "networkApiVersion": "2017-06-01",

  "publicIPAddressType": "Static",

  "vNetRGName": "<RG-WITH-EXISTING-VNET>",

  "vNetName": "<VNET-NAME>",
  "vNetAddressPrefix": "10.0.0.0/16",

  "vNetSubnets": [
    {
      "name": "subnet-ext",
      "prefix": "10.0.1.0/24",
    },
    {
      "name": "subnet-int",
      "prefix": "10.0.2.0/24",
    }
  ],

  "networkSecurityRules": [
    {
      "ruleName": "SSH-HOST01",
      "ruleDescription": "Allows inbound SSH from specific host",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "22",
      "ruleSourceAddressPrefix": "8.8.8.8/32",
      "ruleDestinationAddressPrefix": "*",
    },
    {
      "ruleName": "SSH-HOST02",
      "ruleDescription": "Allows inbound SSH from specific host",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "22",
      "ruleSourceAddressPrefix": "4.4.4.4/32",
      "ruleDestinationAddressPrefix": "*",
    },
  ],
    } }
```

## Authors
-   [**ParanoidRat**][1]

## License
The work is licensed under the CC-BY-SA 4.0 License. Unless otherwise stated,
modifications are also licensed under the CC-BY-SA 4.0 License. See the
[LICENSE.md](LICENSE.md) file for details and specific licensing of the
modifications.

You should have received a copy of the license along with this work
(see the [CC-BY-SA-4.txt](CC-BY-SA-4.txt) file for details).If not, see
<https://creativecommons.org/licenses/by-sa/4.0/legalcode>.

[1]: https://github.com/ParanoidRat
