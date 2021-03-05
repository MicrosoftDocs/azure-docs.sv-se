---
title: Zone-redundant register för hög tillgänglighet
description: Läs om hur du aktiverar zon-redundans i Azure Container Registry. Skapa ett behållar register eller replikering i en tillgänglighets zon i Azure. Zon redundans är en funktion i Premium service-nivån.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203869"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Aktivera zon redundans i Azure Container Registry för återhämtning och hög tillgänglighet

Förutom [geo-replikering](container-registry-geo-replication.md), som replikerar register data över en eller flera Azure-regioner för att tillhandahålla tillgänglighet och minska svars tiden för regionala åtgärder, Azure Container Registry har stöd för valfri *zon redundans*. [Zon redundans](../availability-zones/az-overview.md#availability-zones) ger återhämtning och hög tillgänglighet till ett register eller en replik resurs (replik) i en angiven region.

Den här artikeln visar hur du konfigurerar en zon – redundant behållar register eller en replik med hjälp av Azure CLI, Azure Portal eller Azure Resource Manager mall. 

Zon redundans är en **förhands gransknings** funktion i tjänst nivån Premium container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Stöds för närvarande i följande regioner: östra USA, östra USA 2, västra USA 2, norra Europa, västra Europa, Östra Japan.
* Region konverteringar till tillgänglighets zoner stöds inte för närvarande. Om du vill aktivera stöd för tillgänglighets zoner i en region måste registret antingen skapas i önskad region, med stöd för tillgänglighets zon aktiverat, eller så måste en replikerad region läggas till med stöd för tillgänglighets zoner aktiverat.
* Zon-redundans kan inte inaktive ras i en region.
* [ACR-aktiviteter](container-registry-tasks-overview.md) har ännu inte stöd för tillgänglighets zoner.

## <a name="about-zone-redundancy"></a>Om zon redundans

Använd Azures [tillgänglighets zoner](../availability-zones/az-overview.md) för att skapa en flexibel och hög tillgänglighet för Azure Container Registry i en Azure-region. Organisationer kan till exempel konfigurera en zon – redundant Azure Container Registry med andra Azure- [resurser som stöds](../availability-zones/az-region.md) för att uppfylla data placering eller andra efterföljandekrav, samtidigt som de ger hög tillgänglighet inom en region.

Azure Container Registry stöder också [geo-replikering](container-registry-geo-replication.md), som replikerar tjänsten över flera regioner, vilket möjliggör redundans och plats för att beräkna resurser på andra platser. Kombinationen av tillgänglighets zoner för redundans inom en region och geo-replikering över flera regioner förbättrar både tillförlitlighet och prestanda i ett register.

Tillgänglighets zoner är unika fysiska platser inom en Azure-region. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Varje zon har ett eller flera data Center som är utrustade med oberoende strömförsörjning, kylning och nätverk. När det har kon figurer ATS för zon redundans replikeras ett register (eller en register replik i en annan region) över alla tillgänglighets zoner i regionen, så att den blir tillgänglig om det finns data Center problem.

## <a name="create-a-zone-redundant-registry---cli"></a>Skapa en zon – redundant register-CLI

Om du vill använda Azure CLI för att aktivera zon redundans måste du ha Azure CLI version 2.17.0 eller senare, eller Azure Cloud Shell. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp för registret.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Skapa Zone-aktiverat register

Kör kommandot [AZ ACR Create](/cli/azure/acr#az_acr_create) för att skapa ett zon redundant register på Premium-tjänstnivå. Välj en region som [har stöd för tillgänglighets zoner](../availability-zones/az-region.md) för Azure Container Registry. I följande exempel är zon redundans aktiverat i regionen *östra* . Mer information `az acr create` om register alternativ finns i kommando hjälpen.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

I kommandot utdata noterar du `zoneRedundancy` egenskapen för registret. När den är aktive rad är registret redundant i zonen:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Skapa zon – redundant replikering

Kör kommandot [AZ ACR Replication Create](/cli/azure/acr/replication#az_acr_replication_create) för att skapa en zon redundant register replik i en region som [stöder tillgänglighets zoner](../availability-zones/az-region.md) för Azure Container Registry, till exempel *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Notera `zoneRedundancy` egenskapen för repliken i kommandots utdata. När den är aktive rad är repliken zon redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Skapa en zon – redundant register – Portal

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. Välj **skapa en resurs**  >  **behållare**  >  **container Registry**.
1. På fliken **grundläggande** , Välj eller skapa en resurs grupp och ange ett unikt register namn. 
1. I **plats** väljer du en region som stöder zon redundans för Azure Container Registry, t. ex. *USA, östra*.
1. I **SKU** väljer du **Premium**.
1. I **tillgänglighets zoner** väljer du **aktive rad**.
1. Du kan också konfigurera ytterligare register inställningar och sedan välja **Granska + skapa**.
1. Välj **skapa** för att distribuera register instansen.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Aktivera zon-redundans i Azure Portal":::

Så här skapar du en zon-redundant replikering:

1. Gå till behållar registret för Premium-nivån och välj **replikeringar**.
1. På kartan som visas väljer du en grön sexhörning i en region som stöder zon redundans för Azure Container Registry, till exempel **västra USA 2**. Eller Välj **+ Lägg till**.
1. I fönstret **skapa replikering** bekräftar du **platsen**. I **tillgänglighets zoner** väljer du **aktive rad** och väljer sedan **skapa**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Aktivera zon-redundant replikering i Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Skapa en zon – redundant register-mall

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om det behövs kör du kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp för registret i en region som [stöder tillgänglighets zoner](../availability-zones/az-region.md) för Azure Container Registry, till exempel *öster*. Den här regionen används av mallen för att ange register platsen.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Distribuera mallen 

Du kan använda följande Resource Manager-mall för att skapa ett zon redundant, geo-replikerat register. Mallen som standard aktiverar zon-redundans i registret och en regional replik. 

Kopiera följande innehåll till en ny fil och spara det med ett fil namn, till exempel `registryZone.json` .

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

Kör följande [AZ distribution Group Create](/cli/azure/group/deployment#az_group_deployment_create) -kommando för att skapa registret med hjälp av föregående mallfil. Ange där det anges:

* ett unikt register namn, eller att distribuera mallen utan parametrar och skapar ett unikt namn för dig
* en plats för repliken som stöder tillgänglighets zoner, till exempel *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

I kommandot utdata noterar du `zoneRedundancy` egenskapen för registret och repliken. När den är aktive rad är varje resurs zon redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [regioner som har stöd för tillgänglighets zoner](../availability-zones/az-region.md).
* Lär dig mer om att skapa [tillförlitlighet](/azure/architecture/framework/resiliency/overview) i Azure.
