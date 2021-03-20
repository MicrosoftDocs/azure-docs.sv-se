---
title: Felsök utgående anslutningar i Azure Load Balancer
description: Lösningar på vanliga problem med utgående anslutningar via Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696361"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Felsöka fel vid utgående anslutningar

Den här artikeln är avsedd att ge lösningar på vanliga problem kan uppstå med utgående anslutningar från en Azure Load Balancer. De flesta problem med utgående anslutningar som kunderna upplever beror på att det finns SNAT-portens slut för ande och tids gränsen för anslutningen leder till att paket släpps. Den här artikeln innehåller steg för att åtgärda de här problemen.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Hantera SNAT (PAT) port överbelastning
[Tillfälliga portar](load-balancer-outbound-connections.md) som används för [Pat](load-balancer-outbound-connections.md) är en exhaustible-resurs, som beskrivs i [en fristående virtuell dator utan en offentlig IP-adress](load-balancer-outbound-connections.md) och [belastningsutjämnad virtuell dator utan en offentlig IP-adress](load-balancer-outbound-connections.md). Du kan övervaka användningen av tillfälliga portar och jämföra med din nuvarande allokering för att fastställa risken för eller för att bekräfta SNAT-uttömden med hjälp av [den här](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) guiden.

Om du vet att du initierar flera utgående TCP-eller UDP-anslutningar till samma mål-IP-adress och port, och du ser att det inte går att använda utgående anslutningar eller om du får hjälp av stöd för att du tar slut på SNAT-portar (förallokerade [tillfälliga portar](load-balancer-outbound-connections.md#preallocatedports) som används av [Pat](load-balancer-outbound-connections.md)) har du flera allmänna alternativ för att minska. Granska de här alternativen och Bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutning kan du använda IP stack-statistik (netstat). Eller så kan det vara bra att följa anslutnings beteenden med hjälp av paket insamlingar. Du kan utföra dessa paket avbildningar i gäst operativ systemet på din instans eller använda [Network Watcher för paket fångst](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Allokera manuellt SNAT-portar för att maximera SNAT-portar per virtuell dator
Som definieras i [förallokerade portar](load-balancer-outbound-connections.md#preallocatedports)allokerar belastningsutjämnaren automatiskt portar baserat på antalet virtuella datorer i Server delen. Detta görs som standard för att säkerställa skalbarhet. Om du känner till det maximala antalet virtuella datorer som du kommer att ha i Server delen kan du manuellt allokera SNAT-portar i varje utgående regel. Om du till exempel vet att du har högst 10 virtuella datorer kan du allokera 6 400 SNAT-portar per virtuell dator i stället för standardvärdet 1 024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Ändra programmet för att återanvända anslutningar 
Du kan minska efter frågan för tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Åter användning av anslutningar är särskilt relevant för protokoll som HTTP/1.1, där åter användning av anslutningar är standard. Och andra protokoll som använder HTTP som transport (till exempel REST) kan ha nytta av turn. 

Åter användning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvänd resultat i mer utförda, mycket effektiva TCP-transaktioner.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Ändra programmet så att anslutningspoolen används
Du kan använda ett schema för anslutningspoolen i ditt program, där förfrågningar distribueras internt över en fast uppsättning anslutningar (varje återanvändning där det är möjligt). Det här schemat begränsar antalet tillfälliga portar som används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genom strömningen av begär Anden genom att tillåta flera samtidiga åtgärder när en enskild anslutning blockeras när en åtgärd svarar.  

Anslutningspoolen kanske redan finns i ramverket som du använder för att utveckla ditt program eller konfigurations inställningarna för ditt program. Du kan kombinera anslutningspoolen med återkoppling av anslutning. Dina flera begär Anden använder sedan ett fast, förutsägbart antal portar till samma mål-IP-adress och port. Förfrågningarna drar också nytta av effektiv användning av TCP-transaktioner som minskar svars tiden och resursutnyttjande. UDP-transaktioner kan också dra nytta av, eftersom hantering av antalet UDP-flöden kan i sin tur undvika avgas villkor och hantera SNAT-port användningen.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Ändra programmet för att använda mindre aggressiva omprövnings logik
När [förallokerade tillfälliga portar](load-balancer-outbound-connections.md#preallocatedports) som används för [Pat](load-balancer-outbound-connections.md) är förbrukade eller om program fel uppstår, kan aggressiva eller råa Force-försök utan minskning och backoff logik orsaka att inaktivitet inträffar eller är kvar. Du kan minska efter frågan för tillfälliga portar genom att använda en mindre aggressiva omprövnings logik. 

Tillfälliga portar har en timeoutvärde på 4 minuter (inte justerbar). Om de nya försöken är för aggressiva har uttömdheten ingen möjlighet att rensa upp sig själv. Därför är det viktigt att du överväger hur – och hur ofta – dina program återförsöks transaktioner är en viktig del av designen.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Tilldela varje virtuell dator en offentlig IP-adress
Om du tilldelar en offentlig IP-adress ändras ditt scenario till [offentlig IP-adress till en virtuell dator](load-balancer-outbound-connections.md). Alla tillfälliga portar i den offentliga IP-adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I stället för scenarier där tillfälliga portar i en offentlig IP-adress delas med alla virtuella datorer som är associerade med respektive backend-pool.) Det finns kompromisser att överväga, till exempel ytterligare kostnad för offentliga IP-adresser och den potentiella effekten av att filtrera ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för webb arbets roller.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Använd flera klient delar
När du använder offentliga Standard Load Balancer tilldelar du [flera IP-adresser för klient delen för utgående anslutningar](load-balancer-outbound-connections.md) och [multiplicerar antalet tillgängliga SNAT-portar](load-balancer-outbound-connections.md#preallocatedports).  Skapa en IP-konfiguration, regel och backend-pool för klient delen för att utlösa programmeringen av SNAT till klient delens offentliga IP-adress.  Regeln behöver inte fungera och en hälso avsökning behöver inte lyckas.  Om du använder flera klient delar för inkommande och samma (i stället för utgående) bör du använda anpassade hälso avsökningar för att säkerställa tillförlitligheten.

>[!NOTE]
>I de flesta fall är uttömda SNAT-portar ett tecken på felaktig design.  Se till att du förstår varför du tar slut på portar innan du använder fler klient delar för att lägga till SNAT-portar.  Du kan maskera ett problem som kan leda till fel senare.

## <a name="scale-out"></a><a name="scaleout"></a>Skala ut
[Förallokerade portar](load-balancer-outbound-connections.md#preallocatedports) tilldelas baserat på storleken på backend-poolen och grupperas i nivåer för att minimera störningar när vissa portar måste allokeras om för att hantera den näst större storleks nivån för backend-poolen.  Du kan ha ett alternativ för att öka användningen av SNAT-porten för en specifik klient del genom att skala din backend-pool till den maximala storleken för en specifik nivå.  Tänk på att standard port tilldelningen krävs för att programmet ska kunna skalas ut effektivt utan risk för SNAT-belastning.

Två virtuella datorer i backend-poolen skulle t. ex. ha 1024 SNAT-portar tillgängliga per IP-konfiguration, vilket ger totalt 2048 SNAT-portar för distributionen.  Om distributionen skulle höjas till 50 virtuella datorer, även om antalet förallokerade portar är konstant per virtuell dator, kan totalt 51 200 (50 x 1024) SNAT-portar användas av distributionen.  Om du vill skala ut distributionen kontrollerar du antalet [förallokerade portar](load-balancer-outbound-connections.md#preallocatedports) per nivå för att se till att du formar din utskalning till max värdet för respektive nivå.  I föregående exempel, om du har valt att skala ut till 51 i stället för 50-instanser, skulle du gå vidare till nästa nivå och få färre SNAT-portar per virtuell dator och totalt.

Om du skalar ut till nästa större storleks nivå för backend-poolen finns det möjlighet för några av dina utgående anslutningar att ta tid ut om allokerade portar måste allokeras om.  Om du bara använder några av dina SNAT-portar är det inconsequential att skala ut över nästa större storlek för backend-poolen.  Hälften av de befintliga portarna allokeras om varje gång som du flyttar till nästa nivå i backend-poolen.  Om du inte vill att detta ska ske måste du forma distributionen till nivå storleken.  Eller kontrol lera att programmet kan identifiera och försök igen om det behövs.  TCP keepalive-tillägg kan hjälpa till att upptäcka när SNAT-portar inte längre fungerar på grund av att de allokeras om.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Använd keepalive för att återställa tids gränsen för utgående inaktivitet
Utgående anslutningar har en tids gräns på 4 minuter. Denna timeout är justerbar via [utgående regler](outbound-rules.md). Du kan också använda transport (till exempel TCP keepalive) eller keepalive för program lager för att uppdatera ett inaktivt flöde och återställa denna tids gräns vid behov.  

När du använder TCP keepalive-åtgärder räcker det att aktivera dem på ena sidan av anslutningen. Det räcker till exempel att aktivera dem på Server sidan för att återställa flödets inaktivitet och det är inte nödvändigt för båda sidorna att initiera TCP keepalive.  Det finns liknande koncept för program lager, inklusive databas klient server konfiguration.  Kontrol lera Server sidan för vilka alternativ som finns för programspecifika keepalive-objekt.

## <a name="next-steps"></a>Nästa steg
Vi vill alltid förbättra upplevelsen av våra kunder. Om du har problem med utgående anslutningar som inte finns med i listan eller har lösts av den här artikeln skickar du feedback via GitHub längst ned på den här sidan och vi kommer att adressera din feedback så snart som möjligt.