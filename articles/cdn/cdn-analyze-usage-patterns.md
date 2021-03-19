---
title: Kärn rapporter från Verizon | Microsoft Docs
description: Lär dig att komma åt och Visa Verizon Core-rapporter via hanterings portalen för Verizon-profiler.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90985544"
---
# <a name="core-reports-from-verizon"></a>Core-rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Med hjälp av Verizon Core-rapporter via hanterings portalen för Verizon-profiler kan du Visa användnings mönster för CDN med följande rapporter:

* Bandbredd
* Överförda data
* Träffar
* Cache status
* Cacheträffsförhållande
* Överförda IPV4/IPV6-data

## <a name="accessing-verizon-core-reports"></a>Åtkomst till Verizon Core-rapporter
1. På bladet CDN-profil klickar du på knappen **Hantera** .
   
    ![Knappen Hantera CDN-profil](./media/cdn-reports/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **analys** och hovra över utfällda **kärn rapporter** . Klicka på en rapport i menyn.
   
    ![CDN-hanteringsportalen – menyn Core-rapporter](./media/cdn-reports/cdn-core-reports.png)

3. För varje rapport väljer du ett datum intervall i listan **datum intervall** . Du kan antingen välja ett fördefinierat datum intervall, till exempel **idag** eller **den här veckan**, eller så kan du välja **anpassad** och ange ett datum intervall manuellt genom att klicka på kalender ikonerna. 

4. När du har valt ett datum intervall klickar du på **gå** för att generera rapporten. 

4. Om du vill exportera data i Excel-format klickar du på Excel-ikonen ovanför **gå** -knappen.

## <a name="bandwidth"></a>Bandbredd
Bandbredds rapporten består av en graf och en data tabell som anger användningen av CDN-bandbredden för HTTP och HTTPS under en viss tids period, i Mbit/s. Du kan visa bandbredds användningen för alla pop eller för en viss POP. Med den här rapporten kan du Visa trafik toppar och-distribution för pop.

I listan **Edge-noder** väljer du **alla Edge-noder** för att Visa trafik från alla noder eller välja en region.

Rapporten uppdateras var femte minut.

![Bandbredds rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Överförda data
Den här rapporten består av en diagram-och data tabell som anger användning av CDN-trafik för HTTP och HTTPS under en viss tids period, i GB. Du kan visa trafik användningen för alla pop eller för en viss POP. Med den här rapporten kan du Visa trafik toppar och-distribution över pop.

I listan **Edge-noder** väljer du **alla Edge-noder** för att Visa trafik från alla noder eller välja en region.

Rapporten uppdateras var femte minut.

![Överförd data rapport](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Träffar (status koder)
I den här rapporten beskrivs distribution av status koder för förfrågningar för ditt innehåll. Varje begäran om innehåll genererar en HTTP-statuskod. Status koden beskriver hur Edge pop hanterar begäran. Till exempel anger en status kod för 2xx att begäran har betjänats till en klient, medan en 4xx status kod indikerar att ett fel har uppstått. Mer information om HTTP-statuskod finns i [lista över HTTP-statuskod](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Rapporten träffar](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Cache status
I den här rapporten beskrivs distributionen av cacheträffar och Cachemissar för klient begär Anden. Eftersom det snabbaste prestanda resultatet från cacheträffar kan du optimera data leverans hastigheten genom att minimera Cachemissar och utgångna cacheträffar. 

Om du vill minska cache-missar konfigurerar du din ursprungs server så att du kan minimera användningen av följande: 
 * `no-cache` svarshuvuden
 * Cachelagring av frågesträngar, såvida det inte är absolut nödvändigt  
 * Svars koder som inte är cache-cache

Om du vill minska antalet utgångna cacheträffar ställer du in en till gång till `max-age` en lång period för att minimera antalet begär anden till ursprungs servern.

![Status rapport för cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Huvudets cache-status är:
* TCP_HIT: hanteras från Edge-servern. Objektet fanns i cachen och har inte överskridit sin högsta ålder.
* TCP_MISS: hanteras från ursprungs servern. Objektet fanns inte i cachen och svaret var tillbaka till sitt ursprung.
* TCP_EXPIRED _MISS: hanteras från ursprungs servern efter omverifieringen med ursprung. Objektet fanns i cachen, men överskred sin högsta ålder. En omverifiering med ursprung resulterade i att cacheobjektet ersattes med ett nytt svar från ursprung.
* TCP_EXPIRED _HIT: hanteras från kanten efter omverifieringen med ursprung. Objektet var i cache men hade överskridit sin högsta ålder. En omverifiering med ursprungs servern resulterade i att cacheobjektet ändrades.

### <a name="full-list-of-cache-statuses"></a>Fullständig lista över cache-status
* TCP_HIT – den här statusen rapporteras när en begäran betjänas direkt från POP till klienten. En till gång betjänas direkt från en POP när den cachelagras på den POP som är närmast klienten och har en giltig TTL-värde (Time to Live). TTL bestäms av följande svarshuvuden:
  
  * Cache-Control: s-MaxAge
  * Cache-kontroll: högsta ålder
  * Upphör
* TCP_MISS: denna status anger att en cachelagrad version av den begärda till gången inte hittades på den POP som är närmast klienten. Till gången begärs antingen från en ursprungs Server eller en ursprungs skärms Server. Om ursprungs servern eller ursprungs skärms servern returnerar en till gång, hanteras den till klienten och cachelagras på både klienten och Edge-servern. Annars returneras en status kod som inte är 200 (till exempel 403 förbjuden eller 404).
* TCP_EXPIRED_HIT: den här statusen rapporteras när en begäran som riktar sig mot en till gång med ett utgånget TTL-värde betjänades direkt från POP till klienten. Till exempel när till gångens max ålder har upphört att gälla. 
  
   En förfallen begäran resulterar vanligt vis i en revaliditets-begäran till ursprungs servern. För att en TCP_EXPIRED_HIT status ska ske måste ursprungs servern ange att det inte finns någon nyare version av till gången. Den här situationen resulterar vanligt vis i en uppdatering av till gångens Cache-Control och Expires-huvuden.
* TCP_EXPIRED_MISS: den här statusen rapporteras när en nyare version av en utgången cachelagrad till gång hanteras från POP till klienten. Den här statusen inträffar när TTL för en cachelagrad till gång har upphört att gälla (till exempel utgånget max ålder) och ursprungs servern returnerar en nyare version av till gången. Den här nya versionen av till gången hanteras till klienten i stället för den cachelagrade versionen. Dessutom cachelagras den på Edge-servern och klienten.
* CONFIG_NOCACHE: denna status anger att en kundspecifik konfiguration av Edge POP hindrade till gången från att cachelagras.
* INGEN – denna status anger att det inte utfördes någon uppdaterings kontroll för cache-innehåll.
* TCP_CLIENT_REFRESH_MISS: den här statusen rapporteras när en HTTP-klient, till exempel en webbläsare, tvingar fram en Edge-POP för att hämta en ny version av en inaktuell till gång från ursprungs servern. Som standard hindrar servrarna en HTTP-klient från att tvinga gräns servrarna att hämta en ny version av till gången från ursprungs servern.
* TCP_PARTIAL_HIT: den här statusen rapporteras när ett byte Range-begäran resulterar i en träff för en delvis cachelagrad till gång. Det begärda byte-intervallet betjänas direkt från POP till klienten.
* Det går inte att CACHELAGRA: den här statusen rapporteras när en till gångs- `Cache-Control` och `Expires` -rubriker anger att den inte ska cachelagras på en pop-eller http-klient. Dessa typer av begär Anden hanteras från ursprungs servern.

## <a name="cache-hit-ratio"></a>Cacheträffsförhållande
Den här rapporten anger procent andelen cachelagrade begär Anden som hanterades direkt från cachen.

Rapporten innehåller följande information:

* Det begärda innehållet cachelagrades i den POP som är närmast beställaren.
* Begäran betjänades direkt från kanten på vårt nätverk.
* Begäran krävde inte en omverifiering med ursprungs servern.

Rapporten innehåller inte:

* Begär Anden som nekas på grund av filtrerings alternativ för land/region.
* Begär Anden för till gångar vars huvuden visar att de inte ska cachelagras. Till exempel, `Cache-Control: private` , `Cache-Control: no-cache` eller- `Pragma: no-cache` rubriker förhindrar att en till gång cachelagras.
* Byte intervall begär Anden om delvis cachelagrat innehåll.

Formeln är: (TCP_ träff/(TCP_ träff + TCP_MISS)) * 100

![Kvot rapport över cacheträffar](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Överförda IPV4/IPV6-data
Den här rapporten visar distributionen av trafik användning i IPV4 vs IPV6.

![Överförda IPV4/IPV6-data](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Överväganden
Rapporter kan bara genereras under de senaste 18 månaderna.

