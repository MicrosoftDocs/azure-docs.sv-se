---
title: Distribuera Azure API Management Services till flera Azure-regioner
titleSuffix: Azure API Management
description: Lär dig hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065462"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management stöder distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API Management-tjänst i valfritt antal Azure-regioner som stöds. Funktionen flera regioner hjälper till att minska svars tiden för förfrågningar som uppfattas av geografiskt distribuerade API-konsumenter och förbättrar tjänstens tillgänglighet om en region kopplas från.

En ny Azure API Management-tjänst som ursprungligen bara innehåller en [enhet][unit] i en enda Azure-region, den primära regionen. Ytterligare enheter kan läggas till i primär eller sekundär region. En API Management Gateway-komponent distribueras till varje vald primär och sekundär region. Inkommande API-begäranden dirigeras automatiskt till den närmaste regionen. Om en region försätts i offlineläge dirigeras API-begäranden automatiskt runt den felande regionen till nästa närmaste Gateway.

> [!NOTE]
> Endast Gateway-komponenten i API Management distribueras till alla regioner. Service Management-komponenten och Developer-portalen finns endast i den primära regionen. Därför kommer åtkomst till Developer-portalen och möjligheten att ändra konfigurationen (t. ex. att lägga till API: er, tillämpa principer) att frigöras tills den primära regionen är online igen, om det uppstår avbrott i den primära regionen. Även om den primära regionen är offline kan de sekundära regionerna fortsätta att betjäna API-trafik med den senaste konfigurationen som är tillgänglig för dem.

>[!IMPORTANT]
> Funktionen för att aktivera lagring av kund information i en enda region är för närvarande bara tillgänglig i Sydostasien region (Singapore) för Asien och stillahavsområdet geo. För alla andra regioner lagras kund information på Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>Distribuera API Management tjänst till en ny region

> [!NOTE]
> Om du ännu inte har skapat en API Management tjänst instans, se [skapa en API Management tjänst instans][create an api management service instance].

1. I Azure Portal navigerar du till API Management-tjänsten och klickar på posten **platser** på menyn.
2. Klicka på **+ Lägg till** i det översta fältet.
3. Välj plats i list rutan och ange antalet enheter med skjutreglaget.
4. Klicka på knappen **Lägg till** för att bekräfta.
5. Upprepa processen tills du har konfigurerat alla platser.
6. Klicka på **Spara** i det översta fältet för att starta distributions processen.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Ta bort en API Management tjänst plats

1. I Azure Portal navigerar du till API Management-tjänsten och klickar på posten **platser** på menyn.
2. För den plats som du vill ta bort öppnar du snabb menyn med knappen **...** längst till höger i tabellen. Välj alternativet **ta bort** .
3. Bekräfta borttagningen och klicka på **Spara** för att tillämpa ändringarna.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Dirigera API-anrop till regionala Server dels tjänster

Azure API Management innehåller bara en URL för Server del tjänsten. Även om det finns Azure API Management-instanser i olika regioner vidarebefordrar API-gatewayen förfrågningar till samma server dels tjänst, som distribueras i endast en region. I det här fallet kommer prestanda ökningen bara att komma från svar som cachelagrats i Azure API Management i en region som är speciell för begäran, men att kontakta Server delen över hela världen kan fortfarande orsaka hög latens.

För att helt kunna utnyttja den geografiska distributionen av systemet bör Server dels tjänster distribueras i samma regioner som Azure API Management-instanser. Sedan kan du med hjälp av principer och `@(context.Deployment.Region)` Egenskaper dirigera trafiken till lokala instanser av Server delen.

1. Gå till Azure API Management-instansen och klicka på **API: er** på den vänstra menyn.
2. Välj önskat API.
3. Klicka på **kod redigeraren** i list rutan pil i den **inkommande bearbetningen**.

    ![API-kod redigerare](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Använd `set-backend` kombinerat med villkors `choose` principer för att skapa en korrekt routningsprincip i `<inbound> </inbound>` avsnittet i filen.

    Exempelvis skulle XML-filen nedan fungera för USA, västra och Asien, östra regioner:

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
> Du kan också använda backend-tjänster med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), dirigera API-anrop till Traffic Manager och låta den matcha routningen automatiskt.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Använda anpassad routning för att API Management regionala gateways

API Management dirigerar begär anden till en regional _Gateway_ baserat på [den lägsta svars tiden](../traffic-manager/traffic-manager-routing-methods.md#performance). Även om det inte går att åsidosätta den här inställningen i API Management kan du använda dina egna Traffic Manager med anpassade regler för routning.

1. Skapa din egen [Azure-Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Om du använder en anpassad domän använder du [den med Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) i stället för tjänsten API Management.
1. [Konfigurera API Management regionala slut punkter i Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionala slut punkterna följer URL-mönstret i `https://<service-name>-<region>-01.regional.azure-api.net` , till exempel `https://contoso-westus2-01.regional.azure-api.net` .
1. [Konfigurera API Management regionala status slut punkter i Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Slut punkterna för regional status följer URL-mönstret i `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` , till exempel `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Ange [routningsmetod](../traffic-manager/traffic-manager-routing-methods.md) för Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
