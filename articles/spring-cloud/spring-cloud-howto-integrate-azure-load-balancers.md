---
title: Självstudie – integrera Azure våren Cloud med lösningar för belastnings utjämning i Azure
description: Så här integrerar du Azure våren Cloud med lösningar för belastnings utjämning i Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 50b09fd82461221ae6cd008f6918ac2f3a26fd94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588396"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrera Azure Spring Cloud med Azure Load Balance Solutions

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Azure våren Cloud stöder mikrotjänster på Azure.  Att öka verksamheten kan kräva flera data Center med hantering av flera instanser av Azure våren Cloud.

Azure tillhandahåller redan olika belastnings Utjämnings lösningar. Det finns tre alternativ för att integrera Azure våren Cloud med lösningar för belastnings utjämning i Azure:

1.  Integrera Azure våren Cloud med Azure Traffic Manager
2.  Integrera Azure våren Cloud med Azure App Gateway
3.  Integrera Azure våren Cloud med Azures front dörr

## <a name="prerequisites"></a>Förutsättningar

* Azure våren Cloud: [så här skapar du en moln tjänst för Azure våren](./spring-cloud-quickstart.md)
* Azure Traffic Manager: [så här skapar du en Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway: [så här skapar du en Application Gateway](../application-gateway/quick-create-portal.md)
* Azure-front dörr: [så här skapar du en front dörr](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrera Azure våren Cloud med Azure Traffic Manager

Om du vill integrera Azure våren Cloud med Traffic Manager lägger du till dess offentliga slut punkter som Traffic Manager-slutpunkter och konfigurerar sedan den anpassade domänen för både Traffic Manager och Azure våren Cloud.

### <a name="add-endpoint-in-traffic-manager"></a>Lägg till slut punkt i Traffic Manager
Lägg till slut punkter i Traffic Manager:
1.  Ange **typen** *extern slut punkt*.
1.  Inmatat fullständigt kvalificerat domän namn (FQDN) för varje Azure våren Cloud offentlig slut punkt.
1. Klicka på **OK**.

    ![Traffic Manager 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Konfigurera anpassad domän
Så här slutför du konfigurationen:
1.  Logga in på webbplatsen för din domän leverantör och skapa en CNAME-Postmappning från din anpassade domän till Traffic Managers Azure standard domän namn.
1.  Följ anvisningarna [för att lägga till anpassad domän i Azure våren Cloud](spring-cloud-tutorial-custom-domain.md).
1. Lägg till över anpassad domän bindning till Traffic Manager till Azure våren Cloud motsvarande app service och ladda upp SSL-certifikatet där.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrera Azure våren Cloud med Azure App Gateway

Om du vill integrera med moln tjänsten Azure våren slutför du följande konfigurationer:

### <a name="configure-backend-pool"></a>Konfigurera backend-pool
1. Ange **måltypen** som *IP-adress* eller *fullständigt domän namn*.
1. Ange dina offentliga slut punkter för Azure våren Cloud.

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Lägg till anpassad avsökning
1. Välj **hälso avsökningar** och **Lägg** sedan till i dialog rutan Öppna anpassad **avsökning** . 
1. Nyckel punkten är att välja *Ja* för alternativet **Välj värd namn från Server delens http-inställningar** .

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Konfigurera http-inställning
1.  Välj **http-inställningar** och **Lägg** sedan till för att lägga till en http-inställning.
1.  **Åsidosätt med det nya värd namnet:** Välj *Ja*.
1.  **Åsidosätt värdnamn**: Välj **Välj värdnamn från backend-målet**.
1.  **Använd anpassad avsökning**: Välj *Ja* och välj anpassad avsökning som skapats ovan.

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>Konfigurera omskrivnings uppsättning
1.  Välj **omskrivning** och **Skriv om ange** om du vill lägga till en omskrivning.
1.  Välj de routningsregler som dirigerar begär anden till Azure våren Cloud Public-slutpunkter.
1.  På fliken **konfiguration av Skriv regel** väljer du **Lägg till omskrivning-regel**.
1.  **Typ av omskrivning**: Välj **begär ande huvud**
1.  **Åtgärds typ**: Välj **ta bort**
1.  **Rubrik namn**: Välj **gemensam rubrik**
1.  **Gemensamt sidhuvud**: Välj **X-forwarded-proto**

    ![App Gateway 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrera Azure våren Cloud med Azures front dörr

För att integrera med Azure våren Cloud service och konfigurera backend-poolen, 
1. **Lägg till backend-pool**.
1. Ange backend-slutpunkt genom att lägga till värden.

    ![Front dörr 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Ange **Server dels värd typ** som *anpassad värd*.
1.  Indatamängds-FQDN för dina Azure våren Cloud-offentliga slut punkter i **Server dels värd namnet**.
1.  Godkänn standard för **värd huvud för Server delen** , som är samma som **Server dels värd namnet**.

    ![Front dörr 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Nästa steg
* [Så här skapar du en Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Så här skapar du en Application Gateway](../application-gateway/quick-create-portal.md)
* [Så här skapar du en front dörr](../frontdoor/quickstart-create-front-door.md)