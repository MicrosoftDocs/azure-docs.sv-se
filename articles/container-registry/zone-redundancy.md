---
title: Zonredundant register för hög tillgänglighet
description: Läs mer om hur du aktiverar zonredundans i Azure Container Registry. Skapa ett containerregister eller replikering i en Azure-tillgänglighetszon. Zonredundans är en funktion på Premium-tjänstnivån.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479023"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Aktivera zonredundans i Azure Container Registry för återhämtning och hög tillgänglighet

Förutom [geo-replikering](container-registry-geo-replication.md), som replikerar registerdata över en eller flera Azure-regioner för att tillhandahålla tillgänglighet och minska svarstiden för regionala åtgärder, stöder Azure Container Registry valfri zonredundans .  [Zonredundans](../availability-zones/az-overview.md#availability-zones) ger återhämtning och hög tillgänglighet för ett register eller en replikeringsresurs (replik) i en viss region.

Den här artikeln visar hur du ställer in ett zonredundant containerregister eller en replik med hjälp av Azure CLI, Azure Portal eller Azure Resource Manager-mall. 

Zonredundans är **en förhandsgranskningsfunktion** på tjänstnivån premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry tjänstnivåer](container-registry-skus.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Stöds för närvarande i följande regioner: USA, östra, USA, östra 2, USA, västra 2, Europa, norra, Europa, västra, Japan, östra.
* Regionskonverteringar till tillgänglighetszoner stöds inte för närvarande. Om du vill aktivera stöd för tillgänglighetszoner i en region måste registret antingen skapas i önskad region, med tillgänglighetszonstöd aktiverat, eller så måste en replikerad region läggas till med tillgänglighetszonsstöd aktiverat.
* Zonredundans kan inte inaktiveras i en region.
* [ACR-uppgifter](container-registry-tasks-overview.md) har ännu inte stöd för tillgänglighetszoner.

## <a name="about-zone-redundancy"></a>Om zonredundans

Använd [Azure-tillgänglighetszoner](../availability-zones/az-overview.md) för att skapa ett elastiskt Azure-containerregister med hög tillgänglighet i en Azure-region. Organisationer kan till exempel konfigurera ett zonredundant Azure-containerregister med andra [Azure-resurser](../availability-zones/az-region.md) som stöds för att uppfylla datahemhemlighet eller andra efterlevnadskrav, samtidigt som de ger hög tillgänglighet inom en region.

Azure Container Registry också stöd [för geo-replikering,](container-registry-geo-replication.md)som replikerar tjänsten över flera regioner, vilket möjliggör redundans och plats för att beräkna resurser på andra platser. Kombinationen av tillgänglighetszoner för redundans inom en region och geo-replikering i flera regioner förbättrar både tillförlitligheten och prestandan för ett register.

Tillgänglighetszoner är unika fysiska platser i en Azure-region. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Varje zon har ett eller flera datacenter som är utrustade med oberoende ström, kylning och nätverk. När ett register (eller en registerreplik i en annan region) har konfigurerats för zonredundans replikeras det över alla tillgänglighetszoner i regionen, vilket gör det tillgängligt om det uppstår datacenterfel.

## <a name="create-a-zone-redundant-registry---cli"></a>Skapa ett zonredundant register – CLI

Om du vill använda Azure CLI för att aktivera zonredundans behöver du Azure CLI version 2.17.0 eller senare eller Azure Cloud Shell. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du [kommandot az group create](/cli/azure/group#az_group_create) för att skapa en resursgrupp för registret.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Skapa zonaktiverade register

Kör kommandot [az acr create](/cli/azure/acr#az_acr_create) för att skapa ett zonredundant register på Premium-tjänstnivån. Välj en region som stöder [tillgänglighetszoner](../availability-zones/az-region.md) för Azure Container Registry. I följande exempel aktiveras zonredundans i regionen *eastus.* Se `az acr create` kommandohjälpen för fler registeralternativ.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Observera egenskapen för registret i `zoneRedundancy` kommandoutdata. När det här alternativet är aktiverat är registret zonredundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Skapa zonredundant replikering

Kör kommandot [az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) för att skapa en [](../availability-zones/az-region.md) zonredundant registerreplik i en region som stöder tillgänglighetszoner för Azure Container Registry, till exempel *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Observera -egenskapen för repliken `zoneRedundancy` i kommandoutdata. När repliken är aktiverad är den zonredundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Skapa ett zonredundant register – portalen

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. Välj **Skapa en resurs**  >  **Containrar**  >  **Container Registry**.
1. På fliken **Grundläggande inställningar** väljer eller skapar du en resursgrupp och anger ett unikt registernamn. 
1. I **Plats** väljer du en region som stöder zonredundans för Azure Container Registry, till exempel *USA, östra*.
1. I **SKU** väljer du **Premium**.
1. I **Tillgänglighetszoner** väljer du **Aktiverad.**
1. Du kan också konfigurera ytterligare registerinställningar och sedan välja **Granska + skapa.**
1. Välj **Skapa** för att distribuera registerinstansen.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Aktivera zonredundans i Azure Portal":::

Så här skapar du en zonredundant replikering:

1. Gå till containerregistret på Premium-nivån och välj **Replikeringar.**
1. På kartan som visas väljer du en grön sexhörning i en region som stöder zonredundans för Azure Container Registry, till exempel **USA, västra 2.** Eller välj **+ Lägg till**.
1. I fönstret **Skapa replikering** bekräftar du **Platsen**. I **Tillgänglighetszoner** väljer **du Aktiverad** och sedan **Skapa.**

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Aktivera zonredundant replikering i Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Skapa ett zonredundant register – mall

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du [kommandot az group create](/cli/azure/group#az_group_create) för att [](../availability-zones/az-region.md) skapa en resursgrupp för registret i en region som stöder tillgänglighetszoner för Azure Container Registry, till exempel *eastus*. Den här regionen används av mallen för att ange registerplatsen.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Distribuera mallen 

Du kan använda följande Resource Manager för att skapa ett zonredundant, geo-replikerat register. Mallen aktiverar som standard zonredundans i registret och en regional replik. 

Kopiera följande innehåll till en ny fil och spara den med ett filnamn, till exempel `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Kör följande az [deployment group create-kommando](/cli/azure/group/deployment#az_group_deployment_create) för att skapa registret med hjälp av föregående mallfil. Om det anges anger du:

* ett unikt registernamn eller distribuera mallen utan parametrar, så skapas ett unikt namn åt dig
* en plats för repliken som stöder tillgänglighetszoner, till exempel *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Observera egenskapen för registret och `zoneRedundancy` repliken i kommandoutdata. När den är aktiverad är varje resurs zonredundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om regioner [som stöder tillgänglighetszoner.](../availability-zones/az-region.md)
* Läs mer om att skapa för [tillförlitlighet](/azure/architecture/framework/resiliency/overview) i Azure.
