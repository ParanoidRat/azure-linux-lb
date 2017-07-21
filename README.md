# Linux Cluster with LB

This repository contains Azure templates to deploy a load-balanced Linux-based
generic cluster in Microsoft Azure Recourses Manager (ARM).

## Getting Started

## Parameters
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
  "sshPublicKey": "ssh-rsa <SSH-PUB-KEY>",

  "sshPort": "22"
} },

"netConfig": { "value": {
  "networkApiVersion": "2017-06-01",

  "publicIPAddressType": "Static",

  "sshAllowedFrom": [
    "8.8.8.8/32",
    "4.4.4.4/32"
  ],

  "vNetName": "<VNET-NAME>",
  "vNetAddressPrefix": "10.0.0.0/16",

  "vNetSubnets": [
    {
      "name": "subnet-ext",
      "prefix": "10.0.1.0/24",
      "allocateFrom":"10.0.1.4"
    },
    {
      "name": "subnet-int",
      "prefix": "10.0.2.0/24",
      "allocateFrom":"10.0.2.4"
    }
  ]
} }
}
```

## Authors
-   [**ParanoidRat**] [1]

## License
The work is licensed under the CC-BY-SA 4.0 License. Unless otherwise stated,
modifications are also licensed under the CC-BY-SA 4.0 License. See the
[LICENSE.txt](LICENSE.txt) file for details and specific licensing of the
modifications.

You should have received a copy of the license along with this work
(see the [CC-BY-SA-4.txt](CC-BY-SA-4.txt) file for details).If not, see
<https://creativecommons.org/licenses/by-sa/4.0/legalcode>.

[1]: https://github.com/ParanoidRat
