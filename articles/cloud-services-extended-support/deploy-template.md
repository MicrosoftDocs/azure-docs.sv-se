---
title: Distribuera en Azure Cloud Service (utökad support) – Mallar
description: Distribuera en Azure Cloud Service (utökad support) med hjälp av ARM-mallar
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1e2a0859227ad790763dc9ae07cb408a72538f90
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773376"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Distribuera en moln tjänst (utökad support) med ARM-mallar

I den här självstudien beskrivs hur du skapar en moln tjänst (utökad support) distribution med [arm-mallar](../azure-resource-manager/templates/overview.md). 

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="before-you-begin"></a>Innan du börjar

1. Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support) och skapa de associerade resurserna.

2. Skapa en ny resurs grupp med hjälp av [Azure Portal](/azure/azure-resource-manager/management/manage-resource-groups-portal) eller [PowerShell](/azure/azure-resource-manager/management/manage-resource-groups-powershell). Det här steget är valfritt om du använder en befintlig resurs grupp.
 
3. Skapa ett nytt lagrings konto med hjälp av [Azure Portal](/azure/storage/common/storage-account-create?tabs=azure-portal) eller [PowerShell](/azure/storage/common/storage-account-create?tabs=azure-powershell). Det här steget är valfritt om du använder ett befintligt lagrings konto.

4. Överför dina tjänst definitions-(. csdef) och tjänst konfigurations filer (. cscfg) till lagrings kontot med hjälp av [Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob), [AzCopy](/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) eller [PowerShell](/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container). Hämta SAS-URI: er för båda filerna som ska läggas till i ARM-mallen senare i den här självstudien.

5. Valfritt Skapa ett nyckel valv och ladda upp certifikaten.

    -  Certifikat kan anslutas till moln tjänster för att möjliggöra säker kommunikation till och från tjänsten. För att kunna använda certifikat måste deras tumavtrycken anges i tjänst konfigurations filen (. cscfg) och överföras till ett nyckel valv. Ett nyckel valv kan skapas via [Azure Portal](/azure/key-vault/general/quick-create-portal) eller [PowerShell](/azure/key-vault/general/quick-create-powershell).
    - Det associerade nyckel valvet måste finnas i samma region och prenumeration som moln tjänsten.
    - Det associerade nyckel valvet för måste vara aktiverat för lämpliga behörigheter så att resursen Cloud Services (utökad support) kan hämta certifikat från Key Vault. Mer information finns i [certifikat och Key Vault](certificates-and-key-vault.md)
    - Nyckel valvet måste refereras i avsnittet OsProfile i ARM-mallen som visas i stegen nedan.

## <a name="deploy-a-cloud-service-extended-support"></a>Distribuera en moln tjänst (utökad support)

> [!NOTE]
> Ett enklare och snabbare sätt att skapa en ARM-mall och parameter fil är via [Azure Portal](https://portal.azure.com). Du kan [Hämta den genererade arm-mallen](generate-template-portal.md) via portalen för att skapa din moln tjänst via PowerShell
 
1. Skapa virtuellt nätverk. Namnet på det virtuella nätverket måste matcha referenserna i tjänst konfigurations filen (. cscfg). Om du använder ett befintligt virtuellt nätverk utelämnar du det här avsnittet från ARM-mallen.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Om du skapar ett nytt virtuellt nätverk lägger du till följande i `dependsOn` avsnittet för att se till att plattformen skapar det virtuella nätverket innan du skapar moln tjänsten.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Skapa en offentlig IP-adress och (valfritt) ange egenskapen DNS-etikett för den offentliga IP-adressen. Om du använder en statisk IP-adress måste du referera till den som en Reserverad IP i tjänst konfigurations filen (. cscfg). Om du använder en befintlig IP-adress hoppar du över det här steget och lägger till information om IP-adressen direkt i konfigurations inställningarna för belastningsutjämnaren i ARM-mallen.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Om du skapar en ny IP-adress lägger du till följande i `dependsOn` avsnittet för att se till att plattformen skapar IP-adressen innan du skapar moln tjänsten.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Skapa ett nätverks profil objekt och koppla den offentliga IP-adressen till belastningsutjämnarens klient del. En belastningsutjämnare skapas automatiskt av plattformen.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Lägg till nyckel valvs referensen i  `OsProfile`   avsnittet i arm-mallen. Key Vault används för att lagra certifikat som är kopplade till Cloud Services (utökad support). Lägg till certifikaten i Key Vault och referera sedan till certifikatets tumavtrycken i tjänst konfigurations filen (. cscfg). Du måste också aktivera Key Vault för lämpliga behörigheter så att Cloud Services (utökad support) resurs kan hämta certifikat som lagras som hemligheter från Key Vault. Nyckel valvet måste finnas i samma region och prenumeration som moln tjänsten och ha ett unikt namn. Mer information finns i [använda certifikat med Cloud Services (utökad support)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault är ARM-resurs-ID: t till ditt nyckel valv. Du hittar den här informationen genom att leta upp resurs-ID: t i avsnittet Egenskaper i nyckel valvet.
    > - certificateUrl kan hittas genom att navigera till certifikatet i nyckel valvet som är märkt som **hemligt ID**.  
   >  - certificateUrl ska vara i formatet https://{endpoin}/hemligheter/{secretname}/{Secret-ID}

5. Skapa en roll profil. Se till att antalet roller, roll namn, antalet instanser i varje roll och storlek är detsamma i avsnittet tjänst konfiguration (. cscfg), tjänst definition (. csdef) och roll profil i ARM-mallen.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. Valfritt Skapa en tilläggs profil för att lägga till tillägg i moln tjänsten. I det här exemplet lägger vi till tillägget för fjärr skrivbord och Windows Azure-diagnostik.
   > [!Note] 
   > Lösen ordet för fjärr skrivbord måste vara mellan 8-123 tecken och måste uppfylla minst tre krav på lösen ords komplexitet från följande: 1) innehåller ett versal tecken 2) innehåller ett gement tecken 3) innehåller en siffer siffra 4). det är inte tillåtet att kontrol lera tecken.

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Granska den fullständiga mallen.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Distribuera mallen och parameter filen (definiera parametrar i mallfilen) för att skapa moln tjänsten (utökad support) distribution. Se dessa [exempel mallar](https://github.com/Azure-Samples/cloud-services-extended-support) som obligatoriska.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Nästa steg 

- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)
