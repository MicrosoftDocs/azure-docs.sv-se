---
title: IP-adresser för Azure API Management-tjänsten | Microsoft Docs
description: Lär dig hur du hämtar IP-adresserna för en Azure API Management-tjänst och när de ändras.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80047732"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adresser för Azure API Management

I den här artikeln beskriver vi hur du hämtar IP-adresserna för Azure API Management-tjänsten. IP-adresser kan vara offentliga eller privata om tjänsten finns i ett virtuellt nätverk.

Du kan använda IP-adresser för att skapa brand Väggs regler, filtrera inkommande trafik till Server dels tjänsterna eller begränsa den utgående trafiken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adresser för API Management-tjänsten

Varje API Management tjänst instans i Developer-, Basic-, standard-eller Premium-nivån har offentliga IP-adresser som endast är exklusivt för den tjänst instansen (de delas inte med andra resurser). 

Du kan hämta IP-adresserna från översikts instrument panelen för resursen i Azure Portal.

![API Management IP-adress](media/api-management-howto-ip-addresses/public-ip.png)

Du kan också hämta dem program mässigt med följande API-anrop:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Offentliga IP-adresser kommer att ingå i svaret:

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

I [flera regionala distributioner](api-management-howto-deploy-multi-region.md)har varje regional distribution en offentlig IP-adress.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adresser för API Management tjänst i VNet

Om din API Management-tjänst finns i ett virtuellt nätverk, kommer den att ha två typer av IP-adresser – offentliga och privata.

Offentliga IP-adresser används för intern kommunikation på port `3443` – för hantering av konfiguration (till exempel via Azure Resource Manager). I den externa VNet-konfigurationen används de också för körnings-API-trafik. När en begäran skickas från API Management till en offentlig Server del (Internet), visas en offentlig IP-adress som källa för begäran.

Privata virtuella IP-adresser (VIP), som **endast** är tillgängliga i det [interna VNet-läget](api-management-using-with-internal-vnet.md), används för att ansluta inifrån nätverket till API Management slut punkter – gateways, Developer-portalen och hanterings planet för Direct API-åtkomst. Du kan använda dem för att konfigurera DNS-poster i nätverket.

Adresser för båda typerna visas i Azure Portal och i svar på API-anropet:

![API Management i VNet IP-adress](media/api-management-howto-ip-addresses/vnet-ip.png)


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

API Management använder en offentlig IP-adress för anslutningar utanför VNet och en privat IP-adress för anslutningar i VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adresser för förbruknings nivå API Management tjänst

Om din API Management-tjänst är en tjänst för förbruknings nivå har den ingen särskild IP-adress. Förbruknings nivå tjänsten körs på en delad infrastruktur och utan en deterministisk IP-adress. 

I trafik begränsnings syfte kan du använda intervallet av IP-adresser för Azure-datacenter. Mer information om exakta steg hittar du i [artikeln Azure Functions dokumentation](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Ändringar i IP-adresserna

På nivåerna Developer, Basic, standard och Premium för API Management är de offentliga IP-adresserna (VIP) statiska för en tjänsts livs längd, med följande undantag:

* Tjänsten tas bort och återskapas sedan igen.
* Tjänst prenumerationen [inaktive](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ras eller [varnas](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (till exempel för inbetalning) och återställs sedan.
* Azure Virtual Network läggs till eller tas bort från tjänsten.
* API Managements tjänsten växlas mellan externt och internt VNet-distributions läge.

I [flera regionala distributioner](api-management-howto-deploy-multi-region.md)ändras den regionala IP-adressen om en region är vacated och sedan återställs.
