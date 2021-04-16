---
title: Stöd för tillgänglighetszoner för Azure API Management
description: Lär dig hur du förbättrar återhämtningen för din Azure API Management-tjänstinstans i en region genom att aktivera zonredundans.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559289"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Stöd för tillgänglighetszoner för Azure API Management 

Den här artikeln visar hur du aktiverar zonredundans för din API Management instans med hjälp av Azure Portal. [Zonredundans](../availability-zones/az-overview.md#availability-zones) ger återhämtning och hög tillgänglighet till en tjänstinstans i en specifik Azure-region (plats). Med zonredundans replikeras gatewayen och kontrollplanet för din API Management-instans (API för hantering, utvecklarportalen, Git-konfiguration) över datacenter i fysiskt avgränsade zoner, vilket gör den motståndskraftig mot zonfel. 

API Management också stöd för distributioner i flera regioner, vilket bidrar till att minska svarstiden för förfrågningar som uppfattas av geografiskt distribuerade API-konsumenter och förbättrar tillgängligheten för [gatewaykomponenten](api-management-howto-deploy-multi-region.md)om en region går offline. Kombinationen av tillgänglighetszoner för redundans inom en region och distributioner i flera regioner för att förbättra gatewayens tillgänglighet vid ett regionalt avbrott, hjälper till att förbättra både tillförlitligheten och prestandan för din API Management instans.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Regioner som stöds

Konfiguration API Management för zonredundans stöds för närvarande i följande Azure-regioner.

* Australien, östra
* Brasilien, södra
* Kanada, centrala
* Indien, centrala
* East US
* USA, östra 2
* Frankrike, centrala
* Japan, östra
* USA, södra centrala
* Sydostasien
* Storbritannien, södra
* USA, västra 2
* USA, västra 3

## <a name="prerequisites"></a>Förutsättningar

* Om du ännu inte har skapat en API Management-tjänstinstans kan du gå [till Skapa en API Management-tjänstinstans.](get-started-create-service-instance.md) Välj Premium-tjänstnivån.
* Om din API Management-instans distribueras i ett virtuellt nätverk [måste](api-management-using-with-vnet.md)du konfigurera ett virtuellt nätverk, ett undernät och en offentlig IP-adress på en ny plats där du planerar att aktivera zonredundans.

## <a name="enable-zone-redundancy---portal"></a>Aktivera zonredundans – portalen

I portalen kan du också aktivera zonredundans när du lägger till en plats i API Management-tjänsten eller uppdaterar konfigurationen av en befintlig plats.

1. I Azure Portal navigerar du till API Management tjänst och **väljer Platser** på menyn.
1. Välj en befintlig plats eller välj **+ Lägg till** i det översta fältet. Platsen måste ha [stöd för tillgänglighetszoner](#supported-regions).
1. Välj antalet **[skalningsenheter](upgrade-and-scale.md)** på platsen.
1. I **Tillgänglighetszoner** väljer du en eller flera zoner. Antalet valda enheter måste fördelas jämnt mellan tillgänglighetszonerna. Om du till exempel har valt 3 enheter väljer du 3 zoner så att varje zon är värd för en enhet.
1. Om instansen API Management distribueras i ett [virtuellt nätverk](api-management-using-with-vnet.md)konfigurerar du inställningar för virtuellt nätverk på platsen. Välj ett befintligt virtuellt nätverk, undernät och en offentlig IP-adress som är tillgängliga på platsen.
1. Välj **Använd** och välj sedan **Spara.**

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Aktivera zonredundans":::

> [!IMPORTANT]
> Den offentliga IP-adressen på platsen ändras när du aktiverar, lägger till eller tar bort tillgänglighetszoner. När du uppdaterar tillgänglighetszoner i en region med nätverksinställningar måste du konfigurera en annan offentlig IP-adressresurs än den du konfigurerade tidigare.

> [!NOTE]
> Det kan ta 15 till 45 minuter att tillämpa ändringen på API Management instansen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att distribuera en Azure API Management service-instans till flera Azure-regioner.](api-management-howto-deploy-multi-region.md)
* Du kan också aktivera zonredundans med hjälp av [en Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Läs mer om [Azure-tjänster som stöder tillgänglighetszoner.](../availability-zones/az-region.md)
* Läs mer om att skapa för [tillförlitlighet](/azure/architecture/framework/resiliency/overview) i Azure.