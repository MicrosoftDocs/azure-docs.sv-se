---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 4344cd38d9a58eec27c6202e81b8ef678a510681
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176016"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

Application Gateway kan fatta beslut om routning baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värd rubriker. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` finns i URL dirigeras trafiken till en annan pool som är optimerad för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. 
> * Om du vill göra DNS-baserad global Routning och **inte** har kraven för Transport Layer Security (TLS) protokoll terminering ("SSL-avlastning"), per http/https-begäran eller bearbetning av program lager, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Om du behöver optimera en global routning av din webb trafik och optimera prestanda och tillförlitlighet på toppnivå med hjälp av snabb global redundans, se [front dörren](../frontdoor/front-door-overview.md).
> * Om du vill använda belastnings utjämning för nätverks lager granskar du [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera de här lösningarna efter behov.
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="features"></a>Funktioner

Mer information om Application Gateway funktioner finns i [Azure Application Gateway-funktioner](features.md).

## <a name="pricing-and-sla"></a>Priser och service nivå avtal

Application Gateway pris information finns i [Application Gateway prissättning](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway SLA-information finns i [Application Gateway Service avtal](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Nyheter

Information om vad som är nytt med Azure Application Gateway finns i [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa ett test Application Gateway med hjälp av antingen Azure Portal, Azure PowerShell eller Azure CLI.

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)