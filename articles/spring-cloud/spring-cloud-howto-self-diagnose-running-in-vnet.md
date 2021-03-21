---
title: Så här identifierar du Azure våren Cloud VNET
description: Lär dig att själv diagnostisera och lösa problem i Azure våren-molnet som körs i VNET.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125104"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Själv diagnostisera Azure våren Cloud i VNET
Azure våren Cloud Diagnostics stöder interaktiva fel söknings program som körs i virtuella nätverk utan konfiguration. Azure våren Cloud Diagnostics identifierar problem och vägleder dig till information som hjälper dig att felsöka och lösa dem.

## <a name="navigate-to-the-diagnostics-page"></a>Gå till sidan diagnostik
Följande procedur startar diagnostik för nätverksanslutna program.
1. Logga in på Azure-portalen.
1. Gå till översikts sidan för Azure våren-molnet.
1. Välj **diagnostisera och lös problem** i menyn i det vänstra navigerings fönstret.
1. Välj den tredje kategorin, **nätverk**.

   ![Rubrik för Self-diagnostik](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostisk rapport
När du har klickat på kategorin **nätverk** kan du Visa två problem som rör nätverk som är specifika för ditt VNet inmatade Azure våren Cloud: **DNS-matchning** och **nödvändig utgående trafik**.

   ![Alternativ för själv diagnos](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Välj mål problemet för att Visa diagnostikrapporten. En sammanfattning av diagnostik kommer att visas, till exempel: 

* *Resursen har tagits bort.*
* *Resursen har inte distribuerats i ditt eget virtuella nätverk*.

Vissa resultat innehåller relaterad dokumentation. I olika undernät visas resultaten separat.

## <a name="dns-resolution"></a>DNS-matchning 
Om du väljer **DNS-matchning**, indikerar resultat om det finns DNS-problem med program.  Felfria appar visas på följande sätt:

* *DNS-problem lösta utan problem i under nätet ' subnet01 '*.
* *DNS-problem lösta utan problem i under nätet ' subnet02 '*.

I följande diagnostiska rapport exempel visas att hälso tillståndet för programmet är okänt. Tids ramen för rapporten innehåller inte den tidpunkt då hälso tillståndet rapporterades.  Anta att kontext slut tiden är *2021-03-03T04:20:00Z*. Den senaste tidsstämpeln i **tabellen med DNS-matchning** är *2021-03-03T03:39:00Z*, föregående dag. Hälso kontroll loggen kanske inte har skickats ut på grund av ett blockerat nätverk. 

De okända hälso status resultaten innehåller relaterad dokumentation.  Du kan klicka på vänster vinkel paren tes för att Visa List rutan.

   ![DNS-okänd](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Om du felkonfigurerade din Privat DNS zon post uppsättning får du ett kritiskt resultat som: `Failed to resolve the Private DNS in subnet xxx` . 

I list rutan **DNS-matchnings tabell åter givning** hittar du information om detalj meddelandet som du kan använda för att kontrol lera konfigurationen.

## <a name="required-outbound-traffic"></a>Nödvändig utgående trafik 

Om du väljer **obligatorisk utgående trafik** anger resultatet om det finns utgående trafik problem med program.  Felfria appar visas på följande sätt:

* * Nödvändig utgående trafik har lösts utan problem i under nätet "subnet01".
* * Nödvändig utgående trafik har lösts utan problem i under nätet "subnet02".

Om ett undernät blockeras av NSG-eller brand Väggs regler, och om du inte har blockerat loggen, kommer du att hitta följande problem. Du kan kontrol lera om du har tittat på eventuella [kund ansvar](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Slut punkten misslyckades](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Om det inte finns några data `Required Outbound Traffic Table Renderings` inom 30 minuter blir resultatet `health status unknown` . Nätverket kanske är blockerat eller så har logg tjänsten stoppats.

   ![Diagnostisk slut punkt okänd](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Se även
* [Så här identifierar du Azure våren Cloud](spring-cloud-howto-self-diagnose-solve.md)