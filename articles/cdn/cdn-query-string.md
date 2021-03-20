---
title: Styra Azure CDN cachelagring med frågesträngar – standard nivå
description: Cachelagring av Azure CDN frågesträngar styr hur filer cachelagras när en webb förfrågan innehåller en frågesträng. I den här artikeln beskrivs cachelagring av frågesträngar i Azure CDN Standard produkter.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018587"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Styra Azure CDN cachelagring med frågesträngar – standard nivå
> [!div class="op_single_selector"]
> * [Standard-nivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN) kan du styra hur filer cachelagras för en webb förfrågan som innehåller en frågesträng. I en webbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckel/värde-par, där fält namnet och dess värde skiljs åt av ett likhets tecken (=). Varje nyckel/värde-par avgränsas med ett et-tecken (&). Till exempel http: \/ /www.contoso.com/content.mov?field1=value1&fält2 = värde2. Om det finns fler än ett nyckel/värde-par i en frågesträng i en begäran spelar det ingen roll. 

> [!IMPORTANT]
> Azure CDN Standard-och Premium-produkter innehåller samma funktioner för cachelagring av frågesträng, men användar gränssnittet är annorlunda. I den här artikeln beskrivs gränssnittet för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon**. För cachelagring av frågesträngar med **Azure CDN Premium från Verizon**, se [kontroll Azure CDN cachelagring med frågesträngar – Premium-nivå](cdn-query-string-premium.md).

Tre lägen för frågesträng är tillgängliga:

- **Ignorera frågesträngar**: standard läge. I det här läget skickar noden CDN-ingångs punkt (POP) fråge strängarna från begär Ande till ursprungs servern på den första begäran och cachelagrar till gången. Alla efterföljande begär Anden för till gången som hanteras från POP ignorerar frågesträngarna tills den cachelagrade till gången upphör att gälla.

- **Kringgå cachelagring för frågesträngar**: i det här läget cachelagras inte begär Anden med frågesträngar i CDN-pop-noden. POP-noden hämtar till gången direkt från ursprungs servern och skickar den till beställaren med varje begäran.

- **Cachelagra varje unik URL**: i det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik till gång med sin egen cache. Till exempel, svaret från ursprungs servern för en begäran till exempel. ashx? q = TEST1 cachelagras på POP-noden och returneras för efterföljande cacheminnen med samma frågesträng. En begäran till exempel. ashx? q = TEST2 cachelagras som en separat till gång med en egen tids-till-Live-inställning.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ska ändras med varje begäran, till exempel ett sessions-ID eller ett användar namn, eftersom det leder till ett lågt cacheträffar.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändra inställningar för cachelagring av frågesträngar för standard-CDN-profiler
1. Öppna en CDN-profil och välj sedan den CDN-slutpunkt som du vill hantera.
   
   ![CDN-profil slut punkter](./media/cdn-query-string/cdn-endpoints.png)
   
2. I den vänstra rutan under inställningar klickar du på **regler för cachelagring**.
   
    ![Knappen CDN-cachelagringsregler](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Välj ett läge för frågesträng i **cachelagring av frågesträngar** och klicka sedan på **Spara**.
   
   ![Alternativ för cachelagring av incdn-frågesträng](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att spridas via Azure CDN kanske inte ändringar i cache-Strängs inställningarna visas direkt:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 



