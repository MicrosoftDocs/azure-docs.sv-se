---
title: Styra Azure CDN cachelagring med frågesträngar – Premium-nivå
description: Cachelagring av Azure CDN frågesträngar styr hur filer cachelagras när en webb förfrågan innehåller en frågesträng. I den här artikeln beskrivs cachelagring av frågesträngar i Azure CDN Premium från Verizon-produkten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018604"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Styra Azure CDN cachelagring med frågesträngar – Premium-nivå
> [!div class="op_single_selector"]
> * [Standard-nivå](cdn-query-string.md)
> * [Premiumnivå](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Översikt
Med Azure Content Delivery Network (CDN) kan du styra hur filer cachelagras för en webb förfrågan som innehåller en frågesträng. I en webbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckel/värde-par, där fält namnet och dess värde skiljs åt av ett likhets tecken (=). Varje nyckel/värde-par avgränsas med ett et-tecken (&). Till exempel http: \/ /www.contoso.com/content.mov?field1=value1&fält2 = värde2. Om det finns fler än ett nyckel/värde-par i en frågesträng i en begäran spelar det ingen roll. 

> [!IMPORTANT]
> Standard-och Premium CDN-produkterna tillhandahåller samma funktioner för cachelagring av frågesträng, men användar gränssnittet är annorlunda. I den här artikeln beskrivs gränssnittet för **Azure CDN Premium från Verizon**. För cachelagring av frågesträngar med Azure CDN Standard produkter, se [kontroll Azure CDN cachelagring med frågesträngar – standard nivå](cdn-query-string.md).
>


Tre lägen för frågesträng är tillgängliga:

- **Standard-cache**: standard läge. I det här läget skickar noden CDN-ingångs punkt (POP) fråge strängarna från begär Ande till ursprungs servern på den första begäran och cachelagrar till gången. Alla efterföljande begär Anden för till gången som hanteras från POP-servern ignorerar frågesträngarna tills den cachelagrade till gången upphör att gälla.

    >[!IMPORTANT] 
    > Om token-auktorisering har Aktiver ATS för en sökväg i det här kontot är standard-cache läget det enda läge som kan användas. 

- **no-cache**: i det här läget cachelagras inte begär Anden med frågesträngar i CDN-pop-noden. POP-noden hämtar till gången direkt från ursprungs servern och skickar den till beställaren med varje begäran.

- **unikt cacheminne**: i det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik till gång med sin egen cache. Till exempel, svaret från ursprungs servern för en begäran till exempel. ashx? q = TEST1 cachelagras på POP-noden och returneras för efterföljande cacheminnen med samma frågesträng. En begäran till exempel. ashx? q = TEST2 cachelagras som en separat till gång med en egen tids-till-Live-inställning.
   
    >[!IMPORTANT] 
    > Använd inte det här läget när frågesträngen innehåller parametrar som ska ändras med varje begäran, till exempel ett sessions-ID eller ett användar namn, eftersom det leder till ett lågt cacheträffar.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Ändra inställningar för cachelagring av frågesträngar för Premium CDN-profiler
1. Öppna en CDN-profil och klicka sedan på **Hantera**.
   
    ![Knappen Hantera CDN-profil](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **http-stor** och hovra sedan över menyn med utfällda **cacheinställningar** . Klicka på **sträng för cachelagring av frågor**.
   
    Alternativ för cachelagring av frågesträngar visas.
   
    ![Alternativ för cachelagring av incdn-frågesträng](./media/cdn-query-string-premium/cdn-query-string.png)
3. Välj ett sträng läge för frågesträng och klicka sedan på **Uppdatera**.

> [!IMPORTANT]
> Eftersom det tar tid för registreringen att spridas via CDN, kan det hända att ändringar i cache-strängens inställningar inte visas direkt. Spridningen slutförs vanligt vis om 10 minuter.
 

