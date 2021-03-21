---
title: Inkommande/utgående IP-adresser
description: Lär dig hur inkommande och utgående IP-adresser används i Azure App Service när de ändras och hur du hittar adresserna för din app.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591375"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure App Service](overview.md) är en tjänst för flera innehavare, förutom [App Service miljöer](environment/intro.md). Appar som inte finns i en App Service miljö (inte på den [isolerade nivån](https://azure.microsoft.com/pricing/details/app-service/)) delar nätverks infrastruktur med andra appar. Därför kan inkommande och utgående IP-adresser för en app vara olika och kan till och med ändras i vissa situationer.

[App Service miljöer](environment/intro.md) använder dedikerade nätverks infrastrukturer, så appar som körs i en app service-miljö får statiska, dedikerade IP-adresser både för inkommande och utgående anslutningar.

## <a name="how-ip-addresses-work-in-app-service"></a>Hur IP-adresser fungerar i App Service

En App Service App körs i en App Service plan och App Service planer distribueras till en av distributions enheterna i Azure-infrastrukturen (internt kallat ett webb utrymme). Varje distributions enhet tilldelas en uppsättning virtuella IP-adresser, som innehåller en offentlig inkommande IP-adress och en uppsättning [utgående IP-adresser](#find-outbound-ips). Alla App Service planer i samma distributions enhet och App-instanser som körs i dem, delar samma uppsättning virtuella IP-adresser. För en App Service-miljön (en App Service plan på [isolerad nivå](https://azure.microsoft.com/pricing/details/app-service/)) är App Service plan själva distributions enheten, så de virtuella IP-adresserna är avsedda som ett resultat.

Eftersom du inte har behörighet att flytta en App Service plan mellan distributions enheter, förblir de virtuella IP-adresserna som tilldelas till din app samma, men det finns undantag.

## <a name="when-inbound-ip-changes"></a>När inkommande IP-ändringar

Oavsett antalet utskalade instanser har varje app en enskild inkommande IP-adress. Den inkommande IP-adressen kan ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resurs grupp (distributions enhet kan ändras).
- Ta bort den sista appen i en resurs grupp _och_ regions kombination och återskapa den (distributions enheten kan ändras).
- Ta bort en befintlig IP-baserad TLS/SSL-bindning, till exempel vid certifikat förnyelse (se [Förnya certifikat](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Hitta den inkommande IP-adressen

Kör bara följande kommando i en lokal Terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Hämta en statisk inkommande IP-adress

Ibland kanske du vill ha en dedikerad statisk IP-adress för din app. Om du vill hämta en statisk inkommande IP-adress måste du [skydda en anpassad domän](configure-ssl-bindings.md#secure-a-custom-domain). Om du inte behöver TLS-funktioner för att skydda din app kan du till och med Ladda upp ett självsignerat certifikat för den här bindningen. I en IP-baserad TLS-bindning är certifikatet bundet till själva IP-adressen, så App Service etablerar en statisk IP-adress så att den sker. 

## <a name="when-outbound-ips-change"></a>När utgående IP-ändringar ändras

Oavsett antalet utskalade instanser har varje app ett angivet antal utgående IP-adresser vid en angiven tidpunkt. Utgående anslutningar från App Service-appen, till exempel till en backend-databas, använder en av de utgående IP-adresserna som ursprungs-IP-adress. Vilken IP-adress som ska användas väljs slumpmässigt vid körning, så din backend-tjänst måste öppna brand väggen till alla utgående IP-adresser för din app.

Uppsättningen utgående IP-adresser för din app ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resurs grupp (distributions enhet kan ändras).
- Ta bort den sista appen i en resurs grupp _och_ regions kombination och återskapa den (distributions enheten kan ändras).
- Skala din app mellan de lägre nivåerna (**Basic**, **standard** och **Premium**) och **Premium v2** -nivån (IP-adresser kan läggas till i eller subtraheras från uppsättningen).

Du hittar uppsättningen med alla möjliga utgående IP-adresser som din app kan använda, oavsett pris nivå, genom att söka efter `possibleOutboundIpAddresses` egenskapen eller i fältet **ytterligare utgående IP-adresser** på bladet **Egenskaper** i Azure Portal. Se [hitta utgående IP-adresser](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Hitta utgående IP-adresser

Du hittar de utgående IP-adresser som används av din app i Azure Portal genom att klicka på **Egenskaper** i appens vänstra navigering. De visas i fältet **utgående IP-adresser** .

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Om du vill hitta _alla_ möjliga utgående IP-adresser för din app, oavsett pris nivå, klickar du på **Egenskaper** i appens vänstra navigering. De visas i fältet **ytterligare utgående IP-adresser** .

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begränsar inkommande trafik efter Källans IP-adresser.

> [!div class="nextstepaction"]
> [Statiska IP-begränsningar](app-service-ip-restrictions.md)
