---
title: Skapa en mall för Azure Service Fabric-kluster
description: Lär dig hur du skapar en Resource Manager-mall för ett Service Fabric-kluster. Konfigurera säkerhet, Azure Key Vault och Azure Active Directory (Azure AD) för klientautentisering.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260488"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa en Service Fabric Cluster Resource Manager-mall

Ett [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer där dina mikrotjänster distribueras och hanteras. Ett Service Fabric kluster som körs i Azure är en Azure-resurs och distribueras, hanteras och övervakas med hjälp av Resource Manager.  Den här artikeln beskriver hur du skapar en Resource Manager-mall för ett Service Fabric kluster som körs i Azure.  När mallen är klar kan du [distribuera klustret på Azure](service-fabric-cluster-creation-via-arm.md).

Kluster säkerhet konfigureras när klustret först konfigureras och kan inte ändras senare. Innan du konfigurerar ett kluster bör du läsa [Service Fabric kluster säkerhets scenarier][service-fabric-cluster-security]. I Azure använder Service Fabric x509-certifikat för att skydda klustret och dess slut punkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas också att skydda åtkomsten till hanterings slut punkter. Azure AD-klienter och användare måste skapas innan klustret skapas.  Mer information finns [i Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Innan du distribuerar ett produktions kluster för att köra produktions arbets belastningar bör du först läsa [Check listan för produktions beredskap](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Exempel på Resource Manager-mallar finns i [Azure-exemplen på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångs punkt för kluster mal len.

I den här artikeln används mallarna för säker kluster och mallparametrar i [fem noder][service-fabric-secure-cluster-5-node-1-nodetype] . Hämta *azuredeploy.jspå* och *azuredeploy.parameters.jspå* din dator och öppna båda filerna i din favorit text redigerare.

> [!NOTE]
> För nationella moln (Azure Government, Azure Kina, Azure Germany) bör du även lägga till följande `fabricSettings` i mallen: `AADLoginEndpoint` , `AADTokenEndpointFormat` och `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Lägg till certifikat
Du lägger till certifikat i en kluster resurs hanterare-mall genom att referera till nyckel valvet som innehåller certifikat nycklarna. Lägg till dessa nyckel-valv parametrar och värden i en parameter fil för Resource Manager-mall (*azuredeploy.parameters.jspå*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägg till alla certifikat i den virtuella datorns skalnings uppsättning osProfile
Alla certifikat som är installerade i klustret måste konfigureras i avsnittet **osProfile** i skalnings uppsättnings resursen (Microsoft. Compute/virtualMachineScaleSets). Den här åtgärden instruerar resurs leverantören att installera certifikatet på de virtuella datorerna. Den här installationen omfattar både kluster certifikatet och alla program säkerhets certifikat som du planerar att använda för dina program:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurera Service Fabric kluster certifikat

Certifikatet för klientautentisering måste konfigureras i både den Service Fabric kluster resursen (Microsoft. ServiceFabric/Clusters) och Service Fabric tillägget för virtuella dator skalnings uppsättningar i den virtuella datorns skalnings uppsättnings resurs. Med den här ordningen kan Service Fabric Resource Provider konfigurera den för användning av autentisering med kluster och serverautentisering för hanterings slut punkter.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Lägg till certifikat informationen i resursen för skalnings uppsättning för virtuell dator

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Lägg till certifikat informationen i Service Fabric kluster resursen

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägga till Azure AD-konfiguration för att använda Azure AD för klientåtkomst

Du lägger till Azure AD-konfigurationen i en kluster resurs hanterare-mall genom att referera till nyckel valvet som innehåller certifikat nycklarna. Lägg till de Azure AD-parametrar och-värden i en parameter fil för Resource Manager-mall (*azuredeploy.parameters.jspå*). 

> [!NOTE]
> I Linux måste du skapa Azure AD-klienter och användare innan du skapar klustret.  Mer information finns [i Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Fyll i parameter filen med värdena

Använd slutligen värdena från Key Vault-och Azure AD PowerShell-kommandona för att fylla i parameter filen.

Om du planerar att använda Azure Service Fabric RM PowerShell-modulerna behöver du inte fylla i information om kluster certifikatet. Om du vill att systemet ska generera det självsignerade certifikatet för kluster säkerhet behåller du bara dem som null. 

> [!NOTE]
> För att RM-modulerna ska kunna hämta och fylla i dessa tomma parameter värden, matchar parameter namnen mycket namnen nedan

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Om du använder program certifikat eller använder ett befintligt kluster som du har laddat upp till nyckel valvet måste du hämta informationen och fylla i det.

RM-modulerna har inte möjlighet att generera Azure AD-konfigurationen åt dig, så om du planerar att använda Azure AD för klient åtkomst måste du fylla i det.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testa din mall
Använd följande PowerShell-kommando för att testa Resource Manager-mallen med en parameter fil:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och får till gång till krypterings meddelanden använder du "-debug" som ett alternativ.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Följande diagram illustrerar var nyckel valvet och Azure AD-konfigurationen passar in i Resource Manager-mallen.

![Resurs hanterarens beroende karta][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Nästa steg
Nu när du har en mall för klustret kan du lära dig hur du [distribuerar klustret till Azure](service-fabric-cluster-creation-via-arm.md).  Om du inte redan har gjort det läser du check listan för [produktions beredskap](service-fabric-production-readiness-checklist.md) innan du distribuerar ett produktions kluster.

Information om JSON-syntaxen och egenskaperna för de resurser som distribueras i den här artikeln finns i:

* [Microsoft. ServiceFabric/kluster](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/belastningsutjämnare](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
