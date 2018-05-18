# Load-balanced Linux Cluster
This repository contains Azure Resource Manager (ARM) templates to deploy a load-balanced Linux-based generic cluster in Microsoft Azure . It also has
following standalone templates:
-   [standalone-vpn-vmx100.json](standalone-vpn-vmx100.json)
-   [standalone-vpn-csr1000v.json](standalone-vpn-csr1000v.json)

to deploy virtual routers and build VPN tunnels to the environment.

## Deploying Linux Cluster
```
/usr/bin/az group deployment create \
  --resource-group "RG-NAME" \
  --name "DEPLOYMENT-NAME" \
  --template-uri "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master/main.json" \
  --parameters "@/path/to/params.json"
```

### Parameters: Main template
```json
"templateBaseUrl": {"value": "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master"},
"vmNamespace": { "value": "fustercluck" },
"vmCount": { "value": 2 },

"vmConfig": { "value": {
  "computeApiVersion": "2017-03-30",

  "vmSize": "Standard_DS2",

  "avsNewOrExisting": "New",

  "osManagedDiskType": "Premium_LRS",
  "osManagedDiskCache": "ReadWrite",

  "dataManagedDiskEnabledOrDisabled": "Enabled",
  "dataManagedDiskType": "Premium_LRS",
  "dataManagedDiskCount": 4,
  "dataManagedDiskSizeGb": 64,
  "dataManagedDiskCache": "None",

  "imagePublisher": "OpenLogic",
  "imageOffer": "CentOS",
  "imageSKU": "7.4",

  "adminUsername": "<USER-NAME>",
  "sshPublicKey": "ssh-rsa <SSH-PUB-KEY>",

  "tags": {
    "Environment": "Development"
  }
} },

"netConfig": { "value": {
  "networkApiVersion": "2017-06-01",

  "lbManagementOrExternalOrInternalOrNone": "Management",

  "publicIPAddressType": "Static",

  "vNetNewOrExisting": "Existing",
  "vNetRGName": "<VNET-RG-NAME>",

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

  "subnetLbIntIndex": 1,

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
    {
      "ruleName": "AZURE-FABRIC",
      "ruleDescription": "Allows any inbound probes from Azure fabric",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "*",
      "ruleSourceAddressPrefix": "168.63.129.16",
      "ruleDestinationAddressPrefix": "*"
    }
  ],

  "lbProbes": [],
  "lbRules": [],

  "tags": {
    "Environment": "Development"
  }
} }
```

## Deploying Meraki vMX100
```
/usr/bin/az group deployment create \
  --resource-group "RG-NAME" \
  --name "DEPLOYMENT-NAME" \
  --template-uri "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master/standalone-vpn-vmx100.json" \
  --parameters "@/path/to/params.json"
```

### Parameters: Meraki vMX100 template
```json
"templateBaseUrl": { "value": "https://raw.githubusercontent.com/ParanoidRat/azure-linux-lb/master" },

"commonNamespace": { "value": "fustercluck" },

"vmConfig": { "value": {
  "computeApiVersion": "2017-03-30",

  "vmSize": "Standard_D2_v3",

  "osManagedDiskType": "Standard_LRS",
  "osManagedDiskCache": "ReadWrite",

  "imagePublisher": "cisco",
  "imageOffer": "cisco-meraki-vmx100",
  "imageSKU": "vmx100",

  "authToken": "<TOKEN-FROM-MERAKI-DASHBOARD>",

  "adminUsername": "manage_with_meraki_dashboard",
  "adminPassword": "not_used",
  "tags": {
    "Environment": "Development"
  }
} },

"netConfig": { "value": {
  "networkApiVersion": "2017-06-01",

  "publicIPAddressType": "Static",

  "vNetRGName": "<VNET-RG-NAME>",

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

  "deploymentSubnetIndex": 0,

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
    {
      "ruleName": "AZURE-FABRIC",
      "ruleDescription": "Allows any inbound probes from Azure fabric",
      "ruleProtocol": "Tcp",
      "ruleSourcePortRange": "*",
      "ruleDestinationPortRange": "*",
      "ruleSourceAddressPrefix": "168.63.129.16",
      "ruleDestinationAddressPrefix": "*"
    }
  ],
  "tags": {
    "Environment": "Development"
  }
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
The work is licensed under the CC-BY-SA 4.0 License. Unless otherwise stated, modifications are also licensed under the CC-BY-SA 4.0 License. See the [LICENSE.md](LICENSE.md) file for details and specific licensing of the
modifications.

You should have received a copy of the license along with this work
(see the [CC-BY-SA-4.txt](CC-BY-SA-4.txt) file for details).If not, see
<https://creativecommons.org/licenses/by-sa/4.0/legalcode>.

[1]: https://github.com/ParanoidRat
