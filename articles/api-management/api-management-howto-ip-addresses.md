---
title: IP-adresser för Azure API Management service | Microsoft Docs
description: Lär dig hur du hämtar IP-adresserna för en Azure API Management-tjänst och när de ändras.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 5939292b6e810634723fada17521bb227764b989
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534036"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adresser för Azure API Management

I den här artikeln beskriver vi hur du hämtar IP-adresserna för Azure API Management tjänsten. IP-adresser kan vara offentliga eller privata om tjänsten finns i ett virtuellt nätverk.

Du kan använda IP-adresser för att skapa brandväggsregler, filtrera inkommande trafik till servertjänsterna eller begränsa den utgående trafiken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adresser API Management tjänsten

Varje API Management-tjänstinstans på nivån Utvecklare, Basic, Standard eller Premium har offentliga IP-adresser, som endast är exklusiva för den tjänstinstansen (de delas inte med andra resurser). 

Du kan hämta IP-adresserna från översiktsinstrumentpanelen för din resurs i Azure Portal.

![API Management IP-adress](media/api-management-howto-ip-addresses/public-ip.png)

Du kan också hämta dem programmatiskt med följande API-anrop:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Offentliga IP-adresser är en del av svaret:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

I [multi-regionala distributioner har](api-management-howto-deploy-multi-region.md)varje regional distribution en offentlig IP-adress.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adresser för API Management i VNet

Om din API Management tjänst finns i ett virtuellt nätverk har den två typer av IP-adresser – offentliga och privata.

Offentliga IP-adresser används för intern kommunikation på port `3443` – för att hantera konfiguration (till exempel via Azure Resource Manager). I den externa VNet-konfigurationen används de också för KÖRNINGS-API-trafik. När en begäran skickas från API Management till en offentlig (Internetuppriktad) backend visas en offentlig IP-adress som ursprung för begäran.

Privata virtuella IP-adresser (VIP), som endast är tillgängliga i det interna [VNet-läget,](api-management-using-with-internal-vnet.md)används för att ansluta inifrån nätverket till API Management-slutpunkter – gatewayer, utvecklarportalen och hanteringsplanet för direkt API-åtkomst.  Du kan använda dem för att konfigurera DNS-poster i nätverket.

Du ser adresser för båda typerna i Azure Portal och som svar på API-anropet:

![API Management ip-adress för virtuella nätverk](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management använder en offentlig IP-adress för anslutningar utanför det virtuella nätverket och en privat IP-adress för anslutningar i det virtuella nätverket.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adresser för förbrukningsnivån API Management tjänsten

Om din API Management är en förbrukningsnivåtjänst har den ingen dedikerad IP-adress. Förbrukningsnivåtjänsten körs på en delad infrastruktur och utan en deterministisk IP-adress. 

I trafikbegränsningssyfte kan du använda IP-adressintervallet för Azure-datacenter. Se artikeln [Azure Functions för](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) exakta steg.

## <a name="changes-to-the-ip-addresses"></a>Ändringar i IP-adresserna

I nivåerna Developer, Basic, Standard och Premium för API Management är de offentliga IP-adresserna (VIP) statiska under en tjänsts livslängd, med följande undantag:

* Tjänsten tas bort och skapas sedan på ett annat sätt.
* [Tjänstprenumerationen](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) [pausas eller varnas](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (till exempel för utebliven betalning) och återställs sedan.
* Azure Virtual Network läggs till eller tas bort från tjänsten.
* API Management-tjänsten växlas mellan externt och internt VNet-distributionsläge.

I [multi-regionala distributioner](api-management-howto-deploy-multi-region.md)ändras den regionala IP-adressen om en region är utrymd och sedan återställs. Den regionala IP-adressen ändras också när du aktiverar, lägger till eller tar bort [tillgänglighetszoner.](zone-redundancy.md)
