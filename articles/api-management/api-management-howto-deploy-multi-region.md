---
title: Distribuera Azure API Management tjänster till flera Azure-regioner
titleSuffix: Azure API Management
description: Lär dig hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 9546813173e72b1f264c3668ee889bbeea07ce7f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534085"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management stöder distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API Management-tjänst i val av antal Azure-regioner som stöds. Funktionen för flera regioner hjälper till att minska svarstiden för förfrågningar som uppfattas av geografiskt distribuerade API-konsumenter och förbättrar tjänstens tillgänglighet om en region går offline.

En ny Azure API Management-tjänst innehåller inledningsvis bara [en enhet i][unit] en enda Azure-region, den primära regionen. Ytterligare enheter kan läggas till i de primära eller sekundära regionerna. En API Management gateway-komponent distribueras till varje vald primär och sekundär region. Inkommande API-begäranden dirigeras automatiskt till den närmaste regionen. Om en region går offline dirigeras API-begäranden automatiskt runt den misslyckade regionen till nästa närmaste gateway.

> [!NOTE]
> Endast gatewaykomponenten för API Management distribueras till alla regioner. Tjänsthanteringskomponenten och utvecklarportalen finns endast i den primära regionen. I händelse av avbrott i den primära regionen kommer åtkomsten till utvecklarportalen och möjligheten att ändra konfiguration (t.ex. att lägga till API:er, tillämpa principer) att försämras tills den primära regionen är online igen. När den primära regionen är offline fortsätter tillgängliga sekundära regioner att betjäna API-trafiken med hjälp av den senaste konfigurationen som är tillgänglig för dem. Du kan också [aktivera zonrundanlysning](zone-redundancy.md) för att förbättra tillgängligheten och återhämtningen för de primära eller sekundära regionerna.

>[!IMPORTANT]
> Funktionen för att möjliggöra lagring av kunddata i en enda region är för närvarande endast tillgänglig i Sydostasien Region (Singapore) för Asien och stillahavsområdet Geo. För alla andra regioner lagras kunddata i Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>Förutsättningar

* Om du ännu inte har skapat en API Management-tjänstinstans kan du gå [till Skapa en API Management-tjänstinstans.](get-started-create-service-instance.md) Välj Premium-tjänstnivån.
* Om din API Management-instans distribueras i ett virtuellt nätverk [måste](api-management-using-with-vnet.md)du konfigurera ett virtuellt nätverk, ett undernät och en offentlig IP-adress på den plats som du planerar att lägga till.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>Distribuera API Management-tjänst till en ytterligare plats

1. I Azure Portal navigerar du till API Management tjänst och **väljer Platser** i menyn.
1. Välj **+ Lägg** till i det översta fältet.
1. Välj platsen i listrutan.
1. Välj antalet **[skalningsenheter](upgrade-and-scale.md)** på platsen.
1. Du kan också aktivera [**Tillgänglighetszoner**](zone-redundancy.md).
1. Om instansen API Management distribueras i ett [virtuellt nätverk](api-management-using-with-vnet.md)konfigurerar du inställningar för virtuellt nätverk på platsen. Välj ett befintligt virtuellt nätverk, undernät och en offentlig IP-adress som är tillgängliga på platsen.
1. Välj **Lägg till** för att bekräfta.
1. Upprepa den här processen tills du konfigurerar alla platser.
1. Välj **Spara** i det översta fältet för att starta distributionsprocessen.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Ta bort en API Management-tjänstplats

1. I Azure Portal du till din API Management tjänst och klickar **på posten** Platser i menyn.
2. För den plats som du vill ta bort öppnar du snabbmenyn med **knappen ...** till höger i tabellen. Välj alternativet **Ta** bort.
3. Bekräfta borttagningen och klicka **på Spara** för att tillämpa ändringarna.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Dirigera API-anrop till regionala backend-tjänster

Azure API Management bara en url för backend-tjänsten. Även om det finns Azure API Management-instanser i olika regioner vidarebefordrar API-gatewayen fortfarande begäranden till samma backend-tjänst, som endast distribueras i en region. I det här fallet kommer prestandavinsten endast från svar som cachelagras i Azure API Management i en region som är specifik för begäran, men att kontakta backend över hela världen kan fortfarande orsaka hög fördröjning.

Om du vill utnyttja den geografiska distributionen i systemet fullt ut bör du ha backend-tjänster distribuerade i samma regioner som Azure API Management instanser. Sedan kan du med `@(context.Deployment.Region)` hjälp av principer och egenskap dirigera trafiken till lokala instanser av din backend.

1. Gå till din Azure API Management instans och klicka på **API:er** på den vänstra menyn.
2. Välj önskat API.
3. Klicka **på Kodredigeraren** i listrutan pil i **inkommande bearbetning.**

    ![API-kodredigerare](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Använd tillsammans `set-backend` med villkorliga `choose` principer för att skapa en korrekt routningsprincip `<inbound> </inbound>` i avsnittet i filen.

    XML-filen nedan skulle till exempel fungera för usa, västra och Asien, östra regioner:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Du kan också använda servertjänster med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), dirigera API-anropen till Traffic Manager och låta den lösa routningen automatiskt.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Använda anpassad routning för att API Management regionala gatewayer

API Management dirigerar begäranden till en regional _gateway_ baserat [på den lägsta svarstiden](../traffic-manager/traffic-manager-routing-methods.md#performance). Även om det inte går att åsidosätta den här inställningen i API Management kan du använda en egen Traffic Manager med anpassade routningsregler.

1. Skapa en egen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Om du använder en anpassad domän använder [du den med Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) i stället för API Management tjänsten.
1. [Konfigurera de API Management regionala slutpunkterna i Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionala slutpunkterna följer URL-mönstret `https://<service-name>-<region>-01.regional.azure-api.net` för , till exempel `https://contoso-westus2-01.regional.azure-api.net` .
1. [Konfigurera de API Management regionala statusslutpunkterna i Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). De regionala statusslutpunkterna följer URL-mönstret `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` för , till exempel `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Ange [routningsmetoden](../traffic-manager/traffic-manager-routing-methods.md) för Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
