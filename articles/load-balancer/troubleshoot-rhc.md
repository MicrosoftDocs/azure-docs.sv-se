---
title: Felsök Azure Load Balancer problem med resurs hälsa, frontend och backend-tillgänglighet
description: Använd de tillgängliga måtten för att diagnostisera degraderade eller otillgängliga Azure-Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955628"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Felsök problem med resurs hälsa och inkommande tillgänglighet 

Den här artikeln är en guide för att undersöka problem som påverkar tillgänglighet för klient delens IP-och Server dels resurser. 

Resource Health kontrollen (RHC) för Load Balancer används för att fastställa belastnings utjämningens hälso tillstånd. Den analyserar tillgänglighets måttet för data Sök vägen under ett intervall på **2 minuter** för att avgöra om belastnings Utjämnings slut punkter, kombinationerna av klient delens IP-adress och klient dels port med belastnings Utjämnings regler är tillgängliga.

I tabellen nedan beskrivs RHC-logiken som används för att fastställa hälso tillståndet för belastningsutjämnaren.

| Resurs hälso status | Beskrivning |
| --- | --- |
| Tillgänglig | Standard belastnings Utjämnings resursen är felfri och tillgänglig. |
| Degraderad | Din standard belastningsutjämnare har plattforms-eller användar initierade händelser som påverkar prestanda. Datasökvägens tillgänglighet har visat mindre än 90 % men högre än 25 % hälsa i minst två minuter. Du får medelhög prestanda påverkan. 
| Inte tillgänglig | Standard belastnings Utjämnings resursen är inte felfri. Datapath tillgänglighets mått har rapporterat färre 25% hälso tillstånd i minst två minuter. Du får betydande prestanda påverkan eller brist på tillgänglighet för inkommande anslutningar. Det kan finnas användare eller plattforms händelser som orsakar otillgänglighet. |
| Okänt | Resurs hälso status för din standard belastnings Utjämnings resurs har inte uppdaterats eller har inte tagit emot information om tillgänglighet för data Sök vägar under de senaste 10 minuterna. Det här tillståndet bör vara tillfälligt och återspegla rätt status så snart data tas emot. |


## <a name="about-the-metrics-well-use"></a>Om de mått vi använder
De två mått som ska användas är *tillgänglighet för data Sök väg* och *status för hälso avsökning* och det är viktigt att förstå deras innebörd för att härleda korrekta insikter. 

## <a name="data-path-availability"></a>Tillgänglighet för databana
Data Sök vägens tillgänglighets mått genereras av ett TCP-ping var 25: e sekund på alla klient dels portar som har konfigurerat belastnings utjämning och inkommande NAT-regler. Den här TCP-pingen dirigeras sedan till någon av de felfria (avsöknings bara) Server dels instanserna. Om tjänsten tar emot ett svar på pingen betraktas den som lyckad och summan av måttet upprepas en gång, om det Miss lyckas. Antalet för det här måttet är 1/100 av de totala TCP-pingarna per exempel period. Därför vill vi överväga genomsnittet, som visar genomsnittet för summan/antalet för tids perioden. Data Sök vägens tillgänglighets mått som sammanställs av genomsnitt ger oss en procent andel lyckade för TCP-ping på klient delens IP-adress: port för var och en av dina belastnings utjämnings-och inkommande NAT-regler.

## <a name="health-probe-status"></a>Status för hälsoavsökning
Status måttet för hälso avsökningen genereras av ett ping för det protokoll som definieras i hälso avsökningen. Ping skickas till varje instans i backend-poolen och på den port som definierats i hälso avsökningen. För HTTP-och HTTPS-avsökningar kräver en lyckad ping ett HTTP 200 OK-svar, medan TCP-avsökningar betraktas som de ska. Efter varandra följande lyckade eller misslyckade avsökningar avgör om en server dels instans är felfri och kan ta emot trafik för de belastnings Utjämnings regler som backend-poolen är tilldelad. Precis som med data Sök vägs tillgänglighet använder vi den genomsnittliga agg regeringen, som anger det genomsnittliga antalet lyckade/totala pingar under samplings intervallet. Detta status värde för hälso avsökning anger Server delens hälso tillstånd i isoleringen från belastningsutjämnaren genom att avsöka Server dels instanserna utan att skicka trafik via klient delen.

>[!IMPORTANT]
>Status för hälso avsökningen samplas på en minut. Detta kan leda till smärre variationer i ett annat stadigt värde. Om det till exempel finns två server dels instanser, en avsökad och en avläst, kan hälso avsöknings tjänsten fånga in 7 exempel för den felfria instansen och 6 för den felaktiga instansen. Detta leder till ett tidigare stabilt värde på 50 som visas som 46,15 under ett minut intervall. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnostisera degraderade och ej tillgängliga belastningsutjämnare
Som beskrivs i [resurs hälso artikeln](load-balancer-standard-diagnostics.md#resource-health-status)är en degraderad belastningsutjämnare en som visar mellan 25% och 90% data Sök vägs tillgänglighet, och en otillgänglig belastningsutjämnare är en med mindre än 25% tillgänglighet för data Sök vägar, under en period på två minuter. Samma steg kan vidtas för att undersöka det fel du ser i alla hälso avsöknings status eller aviseringar om tillgänglighet för data Sök vägar som du har konfigurerat. Vi kommer att utforska det fall där vi har kontrollerat vår resurs hälsa och hittat vår belastningsutjämnare för att vara otillgänglig med en tillgänglighet för data Sök väg på 0% – vår tjänst är nere.

Först går vi till vyn detaljerade mått för vårt Load Balancer Insights-blad. Du kan göra detta via resurs bladet för belastnings utjämning eller länken i resursens hälso meddelande.  Härnäst navigerar vi till fliken klient del och backend-tillgänglighet och granskar en period på trettio minuter av tids perioden när ett degraderat eller otillgängligt tillstånd har inträffat. Om vi ser att vår data Sök vägs tillgänglighet har varit 0% vet vi att det finns ett problem som förhindrar trafik för alla våra belastnings utjämnings-och inkommande NAT-regler och kan se hur länge den här påverkan har gått ut. 

Nästa steg är att kontrol lera status mått för hälso avsökning för att avgöra om vår data Sök väg är otillgänglig eftersom vi inte har några felfria Server dels instanser för att betjäna trafik. Om vi har minst en felfri backend-instans för alla våra regler för belastnings utjämning och inkommande, vet vi att det inte är vår konfiguration som gör att våra data Sök vägar inte är tillgängliga. Det här scenariot indikerar ett problem med en Azure-plattform, även om det är sällsynt, inte fret om du hittar dem som en automatiserad varning skickas till vårt team för att snabbt lösa alla plattforms problem.

## <a name="diagnose-health-probe-failures"></a>Diagnostisera hälso avsöknings fel
Anta att vi kontrollerar status för hälso avsökning och tar reda på om alla instanser visas som skadade. I den här artikeln förklaras varför vår data Sök väg inte är tillgänglig eftersom trafiken har nogo. Vi bör gå igenom följande check lista för att utesluta vanliga konfigurations fel:
* Kontrol lera CPU-belastningen för dina resurser för att kontrol lera om dina instanser verkligen är felfria
  * Du kan kontrol lera detta 
* Om du använder en HTTP-eller HTTPS-avsöknings kontroll om programmet är felfritt och svarar
  * Verifiera att detta fungerar genom att direkt komma åt programmen via den privata IP-adressen eller offentliga IP-adress på instans nivå som är kopplad till din server dels instans
* Granska de nätverks säkerhets grupper som tillämpas på våra server dels resurser. Se till att det inte finns några regler med högre prioritet än AllowAzureLoadBalancerInBound som kommer att blockera hälso avsökningen
  * Det kan du göra genom att gå till bladet nätverk för dina Server dels datorer eller Virtual Machine Scale Sets
  * Om du tycker att det här NSG problemet är fallet flyttar du den befintliga regeln för att tillåta eller skapar en ny regel med hög prioritet för att tillåta AzureLoadBalancer-trafik
* Kontrol lera operativ systemet. Se till att de virtuella datorerna lyssnar på avsöknings porten och granska deras brand Väggs regler för att säkerställa att de inte blockerar avsöknings trafiken från IP-168.63.129.16
  * Du kan kontrol lera lyssnings portar genom att köra netstat-a Windows-Kommandotolken eller netstat-l i en Linux-terminal
* Placera inte en brand Väggs NVA VM i belastningsutjämnaren för belastningsutjämnaren, Använd [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined) för att dirigera trafik till Server dels instanser via brand väggen
* Se till att du använder rätt protokoll, om du använder HTTP för att avsöka en port som lyssnar efter ett icke-HTTP-program, Miss söker avsökningen

Om du har gått igenom check listan och fortfarande hittar hälso avsöknings fel kan det hända att sällsynta plattforms problem påverkar avsöknings tjänsten för dina instanser. I det här fallet har Azure din tillbaka och en automatiserad avisering skickas till vårt team för att snabbt lösa alla plattforms problem.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om den Azure Load Balancer hälso avsökningen](load-balancer-custom-probe-overview.md)
* [Läs mer om Azure Load Balancer mått](load-balancer-standard-diagnostics.md)
