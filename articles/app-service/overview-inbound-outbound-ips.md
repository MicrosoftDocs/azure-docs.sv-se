---
title: Inkommande/utgående IP-adresser
description: Lär dig hur inkommande och utgående IP-adresser används i Azure App Service, när de ändras och hur du hittar adresserna för din app.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1dda487d23c9f955aea8e35d16e5a560a890a173
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834488"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure App Service](overview.md) är en tjänst för flera innehavare, [förutom App Service miljöer](environment/intro.md). Appar som inte finns i en App Service -miljö (inte på [den isolerade nivån)](https://azure.microsoft.com/pricing/details/app-service/)delar nätverksinfrastrukturen med andra appar. Därför kan de inkommande och utgående IP-adresserna för en app vara olika och kan till och med ändras i vissa situationer.

[App Service-miljöer](environment/intro.md) använder dedikerade nätverksinfrastrukturer, så appar som körs i en App Service-miljö får statiska, dedikerade IP-adresser både för inkommande och utgående anslutningar.

## <a name="how-ip-addresses-work-in-app-service"></a>Så här fungerar IP-adresser i App Service

En App Service-app körs i en App Service plan och App Service-planer distribueras till en av distributionsenheterna i Azure-infrastrukturen (internt kallat ett webbområde). Varje distributionsenhet tilldelas en uppsättning virtuella IP-adresser, som innehåller en offentlig inkommande IP-adress och en uppsättning [utgående IP-adresser.](#find-outbound-ips) Alla App Service i samma distributionsenhet och appinstanser som körs i dem delar samma uppsättning virtuella IP-adresser. För en App Service-miljön (en App Service plan på isolerad nivå [)](https://azure.microsoft.com/pricing/details/app-service/)är App Service plan själva distributionsenheten, så de virtuella IP-adresserna är dedikerade till den.

Eftersom du inte får flytta en App Service plan mellan distributionsenheter förblir de virtuella IP-adresserna som tilldelats din app vanligtvis desamma, men det finns undantag.

## <a name="when-inbound-ip-changes"></a>När inkommande IP ändras

Oavsett antalet utskalade instanser har varje app en enda inkommande IP-adress. Den inkommande IP-adressen kan ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resursgrupp (distributionsenheten kan ändras).
- Ta bort den sista appen i en kombination av _resursgrupp_ och region och återskapa den (distributionsenheten kan ändras).
- Ta bort en befintlig IP-baserad TLS/SSL-bindning, till exempel under certifikatförnyelse (se [Förnya certifikat).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>Hitta den inkommande IP-adressen

Kör bara följande kommando i en lokal terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Hämta en statisk inkommande IP-adress

Ibland kanske du vill ha en dedikerad, statisk IP-adress för din app. För att få en statisk inkommande IP-adress måste du [skydda en anpassad domän.](configure-ssl-bindings.md#secure-a-custom-domain) Om du inte behöver TLS-funktioner för att skydda din app kan du till och med ladda upp ett själv signerat certifikat för den här bindningen. I en IP-baserad TLS-bindning är certifikatet bundet till själva IP-adressen, så App Service till en statisk IP-adress så att den sker. 

## <a name="when-outbound-ips-change"></a>När utgående IP-adresser ändras

Oavsett antalet utskalade instanser har varje app ett anställt antal utgående IP-adresser vid en given tidpunkt. Alla utgående anslutningar från App Service-appen, till exempel till en backend-databas, använder en av de utgående IP-adresserna som den ursprungliga IP-adressen. Ip-adressen som ska användas väljs slumpmässigt vid körning, så din backend-tjänst måste öppna brandväggen för alla utgående IP-adresser för din app.

Uppsättningen utgående IP-adresser för din app ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resursgrupp (distributionsenheten kan ändras).
- Ta bort den sista appen i en kombination av _resursgrupp_ och region och återskapa den (distributionsenheten kan ändras).
- Skala appen mellan de lägre nivåerna **(Basic,** **Standard** och **Premium)** och **Premium V2-nivån** (IP-adresser kan läggas till eller subtraheras från uppsättningen).

Du hittar uppsättningen med alla möjliga utgående IP-adresser som din app kan använda, oavsett prisnivå, genom att leta efter egenskapen eller i fältet Ytterligare utgående `possibleOutboundIpAddresses` **IP-adresser** på bladet Egenskaper i Azure Portal.  Se [Hitta utgående IP-adresser.](#find-outbound-ips)

## <a name="find-outbound-ips"></a>Hitta utgående IP-adresser

Om du vill hitta de utgående IP-adresser som för  närvarande används av din app i Azure Portal klickar du på Egenskaper i appens vänstra navigeringsfält. De visas i fältet **Utgående IP-adresser.**

Du hittar samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Om du _vill_ hitta alla möjliga utgående IP-adresser för  din app, oavsett prisnivå, klickar du på Egenskaper i appens vänstra navigeringsfält. De visas i fältet **Ytterligare utgående IP-adresser.**

Du hittar samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begränsar inkommande trafik efter källans IP-adresser.

> [!div class="nextstepaction"]
> [Statiska IP-begränsningar](app-service-ip-restrictions.md)
