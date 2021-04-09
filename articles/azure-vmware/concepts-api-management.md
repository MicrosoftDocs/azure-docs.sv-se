---
title: Koncept – API Management
description: 'Lär dig hur API Management skyddar API: er som körs på virtuella datorer i Azure VMware-lösningen'
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541971"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management att publicera och skydda API: er som körs på Azure VMware-lösningar baserade på virtuella datorer

Med Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) kan du på ett säkert sätt publicera till interna eller externa användare.  Endast Developer-och Premium-SKU: er tillåter Azure Virtual Network integration att publicera API: er som körs på arbets belastningar för Azure VMware-lösningar.  Båda SKU: erna aktiverar säker anslutning mellan API Management tjänsten och Server delen. 

>[!NOTE]
>Developer SKU är avsedd för utveckling och testning medan Premium-SKU: n är avsedd för produktions distributioner.

API Management konfigurationen är densamma för Server dels tjänster som körs på de virtuella Azure VMware-lösningarna (VM) och lokalt. För båda distributionerna konfigurerar API Management den virtuella IP-adressen (VIP) för belastningsutjämnaren som backend-slutpunkt när backend-servern placeras bakom en NSX Load Balancer på Azure VMware-lösningen. 


## <a name="external-deployment"></a>Extern distribution

En extern distribution publicerar API: er som används av externa användare med hjälp av en offentlig slut punkt. Utvecklare och DevOps-tekniker kan hantera API: er via Azure Portal eller PowerShell och API Management Developer-portalen.

Det externa distributions diagrammet visar hela processen och de aktörer som ingår (visas överst). Aktörerna är:

- **Administratör (er):** Representerar administratörs-eller DevOps-teamet som hanterar Azure VMware-lösningen via Azure Portal-och automation-mekanismer som PowerShell eller Azure DevOps.

- **Användare:**  Representerar de exponerade API: ernas konsumenter och representerar både användare och tjänster som konsumerar API: erna.

Trafikflödet går genom API Management instans, som sammanfattar backend-tjänsterna, anslutna till det virtuella hubb nätverket. ExpressRoute-gatewayen dirigerar trafiken till ExpressRoute Global Reach-kanalen och når en NSX Load Balancer distribuerar inkommande trafik till de olika instanserna för Server dels tjänster.

API Management har ett offentligt Azure-API och aktivering av Azure DDOS Protection-tjänsten rekommenderas. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Extern distribution – API Management för Azure VMware-lösning":::


## <a name="internal-deployment"></a>Intern distribution

En intern distribution publicerar API: er som används av interna användare eller system. DevOps team-och API-utvecklare använder samma hanterings verktyg och utvecklings portal som i den externa distributionen.

Interna distributioner kan göras [med Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) för att skapa en offentlig och säker slut punkt för API: et.  Gatewayens funktioner används för att skapa en hybrid distribution som möjliggör olika scenarier.  

* Använd samma API Management-resurs för användning av både interna och externa konsumenter.

* Ha en enda API Management resurs med en delmängd av API: er definierade och tillgängliga för externa användare.

* Ger ett enkelt sätt att byta åtkomst till API Management från det offentliga Internet på och av.

I distributions diagrammet nedan visas konsumenter som kan vara interna eller externa, och varje typ använder samma eller olika API: er.

I en intern distribution kommer API: er att exponeras för samma API Management-instans. Före API Management är Application Gateway distribueras med WAF-funktionen (Azure Web Application Firewall) aktive rad. Även distribuerad, en uppsättning HTTP-lyssnare och regler för att filtrera trafiken, exponerar bara en delmängd av Server dels tjänsterna som körs på Azure VMware-lösningen.


* Intern trafik dirigeras genom ExpressRoute-gatewayen till Azure-brandväggen och sedan till API Management, direkt eller via trafik regler.   

* Extern trafik går in i Azure via Application Gateway, som använder det externa skydds skiktet för API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Intern distribution – API Management för Azure VMware-lösning" lightbox="media/api-management/internal-deployment.png":::