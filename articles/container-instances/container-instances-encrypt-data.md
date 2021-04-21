---
title: Kryptera distributionsdata
description: Lär dig mer om kryptering av data som bevaras för dina containerinstansresurser och hur du krypterar data med en kund hanterad nyckel
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790877"
---
# <a name="encrypt-deployment-data"></a>Kryptera distributionsdata

När du Azure Container Instances (ACI)-resurser i molnet samlar ACI-tjänsten in och bevarar data relaterade till dina containrar. ACI krypterar automatiskt dessa data när de sparas i molnet. Den här krypteringen skyddar dina data för att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. ACI ger dig också möjlighet att kryptera dessa data med din egen nyckel, vilket ger dig större kontroll över de data som är relaterade till dina ACI-distributioner.

## <a name="about-aci-data-encryption"></a>Om ACI-datakryptering 

Data i ACI krypteras och dekrypteras med 256-bitars AES-kryptering. Den är aktiverad för alla ACI-distributioner och du behöver inte ändra distributionen eller containrarna för att dra nytta av den här krypteringen. Detta inkluderar metadata om distributionen, miljövariabler, nycklar som skickas till dina containrar och loggar som bevaras när containrarna har stoppats så att du fortfarande kan se dem. Krypteringen påverkar inte containergruppens prestanda och det finns ingen extra kostnad för kryptering.

## <a name="encryption-key-management"></a>Hantering av krypteringsnyckel

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina containerdata, eller så kan du hantera krypteringen med dina egna nycklar. I följande tabell jämförs dessa alternativ: 

|    |    Microsoft-hanterade nycklar     |     Kundhanterade nycklar     |
|----|----|----|
|    **Krypterings-/dekrypteringsåtgärder**    |    Azure    |    Azure    |
|    **Nyckellagring**    |    Microsoft Key Store    |    Azure Key Vault    |
|    **Nyckelrotationsansvar**    |    Microsoft    |    Kund    |
|    **Nyckelåtkomst**    |    Endast Microsoft    |    Microsoft, Kund    |

Resten av dokumentet beskriver de steg som krävs för att kryptera dina ACI-distributionsdata med din nyckel (kund hanterad nyckel). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Kryptera data med en kund hanterad nyckel

### <a name="create-service-principal-for-aci"></a>Skapa tjänstens huvudnamn för ACI

Det första steget är att se till att [din Azure-klientorganisation](../active-directory/develop/quickstart-create-new-tenant.md) har ett tilldelat tjänsthuvudnamn för att bevilja behörigheter till Azure Container Instances tjänsten. 

> [!IMPORTANT]
> För att kunna köra följande kommando och skapa ett huvudnamn för tjänsten bekräftar du att du har behörighet att skapa tjänstens huvudnamn i din klientorganisation.
>

Följande CLI-kommando ställer in ACI SP i Azure-miljön:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Utdata från körningen av det här kommandot bör visa ett huvudnamn för tjänsten som har ställts in med "displayName": "Azure Container Instance Service".

Om du inte kan skapa tjänstens huvudnamn:
* bekräfta att du har behörighet att göra det i din klientorganisation
* kontrollera om det redan finns ett huvudnamn för tjänsten i din klient för distribution till ACI. Du kan göra det genom att köra `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` och använda tjänstens huvudnamn i stället

### <a name="create-a-key-vault-resource"></a>Skapa en Key Vault resurs

Skapa en Azure Key Vault med [Azure Portal,](../key-vault/general/quick-create-portal.md) [Azure CLI](../key-vault/general/quick-create-cli.md)eller [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

Använd följande riktlinjer för egenskaperna för ditt nyckelvalv: 
* Namn: Ett unikt namn krävs. 
* Prenumeration: Välj en prenumeration.
* Under Resursgrupp väljer du antingen en befintlig resursgrupp eller skapar en ny och anger ett resursgruppsnamn.
* Välj en plats i listrutan Plats.
* Du kan lämna standardinställningarna för de andra alternativen eller välja dem baserat på ytterligare krav.

> [!IMPORTANT]
> När du använder kund hanterade nycklar för att kryptera en ACI-distributionsmall rekommenderar vi att följande två egenskaper anges i nyckelvalvet, Mjuk borttagning och Rensa inte. Dessa egenskaper är inte aktiverade som standard, men kan aktiveras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckelvalv.

### <a name="generate-a-new-key"></a>Generera en ny nyckel 

När nyckelvalvet har skapats navigerar du till resursen i Azure Portal. I den vänstra navigeringsmenyn på resursbladet går du till Inställningar och klickar på **Nycklar.** I vyn för "Nycklar" klickar du på "Generera/importera" för att generera en ny nyckel. Använd ett unikt namn för den här nyckeln och andra inställningar baserat på dina krav. 

![Generera en ny nyckel](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Ange åtkomstprincip

Skapa en ny åtkomstprincip för att ge ACI-tjänsten åtkomst till din nyckel.

* När nyckeln har genererats går du tillbaka till resursbladet för nyckelvalvet och under Inställningar klickar du **på Åtkomstprinciper.**
* På sidan Åtkomstprinciper för nyckelvalvet klickar du på Lägg **till åtkomstprincip.**
* Ange *nyckelbehörigheter för* att inkludera **behörigheter för att** hämta och packa **upp** ![ nyckeluppsättningsnyckel](./media/container-instances-encrypt-data/set-key-permissions.png)
* För *Välj huvudnamn* väljer du **Azure Container Instance Service**
* Klicka **på Lägg** till längst ned 

Åtkomstprincipen bör nu visas i ditt nyckelvalvs åtkomstprinciper.

![Ny åtkomstprincip](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Ändra JSON-distributionsmallen

> [!IMPORTANT]
> Kryptering av distributionsdata med en kund hanterad nyckel är tillgängligt i den senaste API-versionen (2019-12-01) som för närvarande lanseras. Ange den här API-versionen i distributionsmallen. Om du har problem med detta kan du kontakta Azure Support.

När nyckelvalvsnyckeln och åtkomstprincipen har ställts in lägger du till följande egenskaper i ACI-distributionsmallen. Läs mer om att distribuera ACI-resurser med en mall i [Självstudie: Distribuera](./container-instances-multi-container-group.md)en grupp med flera containrar med en Resource Manager mall . 
* Under `resources` anger du till `apiVersion` `2019-12-01` .
* Under avsnittet egenskaper för containergrupp i distributionsmallen lägger du till `encryptionProperties` en , som innehåller följande värden:
  * `vaultBaseUrl`: DNS-namnet för ditt nyckelvalv finns på översiktsbladet för nyckelvalvsresursen i portalen
  * `keyName`: namnet på den nyckel som genererades tidigare
  * `keyVersion`: den aktuella versionen av nyckeln. Du hittar detta genom att klicka på själva nyckeln (under "Nycklar" i avsnittet Inställningar för nyckelvalvsresursen)
* Under egenskaperna för containergruppen lägger du till en `sku` egenskap med värdet `Standard` . Egenskapen `sku` krävs i API-version 2019-12-01.

Följande mallfragment visar dessa ytterligare egenskaper för att kryptera distributionsdata:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Följande är en fullständig mall som anpassats från mallen i [Självstudie: Distribuera](./container-instances-multi-container-group.md)en grupp med flera containrar med en Resource Manager mall . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Distribuera dina resurser

Om du har skapat och redigerat mallfilen på skrivbordet kan du ladda upp den till Cloud Shell-katalogen genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. När distributionen är klar krypteras alla data som är relaterade till den av ACI-tjänsten med den nyckel som du angav.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
