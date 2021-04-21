---
title: Felsöka utgående anslutningar i Azure Load Balancer
description: Lösningar på vanliga problem med utgående anslutningar via Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 1f52900086afef09d69b80bf44474d5514e25235
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748888"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Felsöka fel med utgående anslutningar

Den här artikeln är avsedd att ge lösningar på vanliga problem som kan uppstå med utgående anslutningar från en Azure Load Balancer. De flesta problem med utgående anslutningar som kunderna upplever beror på att SNAT-porten är slut och att anslutningens tidsgränser har gått ut, vilket leder till ignorerade paket. Den här artikeln innehåller steg för att åtgärda vart och ett av dessa problem.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Hantera SNAT-portutmattning (PAT)
[Tillfälliga portar som används](load-balancer-outbound-connections.md) för [PAT](load-balancer-outbound-connections.md) är en uttömd resurs, enligt beskrivningen i Fristående virtuell dator utan en offentlig [IP-adress](load-balancer-outbound-connections.md) och belastningsutjämnad virtuell dator utan en [offentlig IP-adress.](load-balancer-outbound-connections.md) Du kan övervaka användningen av tillfälliga portar och jämföra med din aktuella allokering för att fastställa risken för eller för att bekräfta SNAT-utmattning med hjälp av den [här](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) guiden.

Om du vet att du initierar många utgående TCP- eller UDP-anslutningar till samma mål-IP-adress och port, och du observerar misslyckade [](load-balancer-outbound-connections.md#preallocatedports) utgående anslutningar, eller om du har stöd för att du använder SNAT-portar (förallokerade tillfälliga portar som används av [PAT)](load-balancer-outbound-connections.md)har du flera allmänna skyddsalternativ. Granska dessa alternativ och bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutningar kan du använda STATISTIK för IP-stack (netstat). Eller så kan det vara bra att observera anslutningsbeteenden med hjälp av paketinfångningar. Du kan utföra dessa paketinfångningar i gästoperativsystemet för din instans eller använda [Network Watcher för infångade paket.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Allokera SNAT-portar manuellt för att maximera SNAT-portar per virtuell dator
Enligt definitionen i [förallokerade](load-balancer-outbound-connections.md#preallocatedports)portar allokerar lastbalanseraren automatiskt portar baserat på antalet virtuella datorer i backend-datorn. Som standard görs detta försiktigt för att säkerställa skalbarhet. Om du vet det maximala antalet virtuella datorer som du kommer att ha i backend-datorn kan du allokera SNAT-portar manuellt i varje regel för utgående trafik. Om du till exempel vet att du har högst 10 virtuella datorer kan du allokera 6 400 SNAT-portar per virtuell dator i stället för standardporten 1 024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Ändra programmet för att återanvända anslutningar 
Du kan minska behovet av tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Återanvändning av anslutningar är särskilt relevant för protokoll som HTTP/1.1, där återanvändning av anslutningar är standard. Och andra protokoll som använder HTTP som transport (till exempel REST) kan i sin tur dra nytta av det. 

Återanvändning är alltid bättre än enskilda atomiska TCP-anslutningar för varje begäran. Återanvändning resulterar i mer effektiva TCP-transaktioner.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Ändra programmet så att det använder anslutningspooler
Du kan använda ett schema för anslutningspooler i ditt program, där begäranden distribueras internt över en fast uppsättning anslutningar (var och en återanvänder där det är möjligt). Det här schemat begränsar antalet tillfälliga portar som används och skapar en mer förutsägbar miljö. Det här schemat kan också öka dataflödet för begäranden genom att tillåta flera samtidiga åtgärder när en enda anslutning blockerar svaret av en åtgärd.  

Det kan redan finnas en anslutningspool i det ramverk som du använder för att utveckla ditt program eller konfigurationsinställningarna för ditt program. Du kan kombinera anslutningspooler med återanvändning av anslutningar. Dina flera begäranden använder sedan ett fast, förutsägbart antal portar till samma mål-IP-adress och port. Begärandena drar också nytta av effektiv användning av TCP-transaktioner som minskar svarstiden och resursanvändningen. UDP-transaktioner kan också dra nytta av detta, eftersom hantering av antalet UDP-flöden i sin tur kan undvika uttömningsvillkor och hantera SNAT-portanvändningen.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Ändra programmet till att använda mindre aggressiv omprövningslogik
När [förallokerade](load-balancer-outbound-connections.md#preallocatedports) tillfälliga portar som används för [PAT](load-balancer-outbound-connections.md) är uttömda eller om programfel inträffar kan aggressivt eller råstyrt framtaktsförsök utan svikt- och backofflogik orsaka att uttömningen uppstår eller kvarstår. Du kan minska behovet av tillfälliga portar genom att använda en mindre aggressiv omprövningslogik. 

Tillfälliga portar har en tidsgräns på 4 minuter för inaktivitet (inte justerbar). Om återförsöken är för aggressivt har uttömningen ingen möjlighet att rensa på egen hand. Med tanke på hur och hur ofta programmet försöker göra transaktioner på nytt är det därför en viktig del av designen.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Tilldela en offentlig IP-adress till varje virtuell dator
Om du tilldelar en offentlig IP-adress ändras ditt scenario till [Offentlig IP-adress till en virtuell dator.](load-balancer-outbound-connections.md) Alla tillfälliga portar för den offentliga IP-adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (Till skillnad från scenarier där tillfälliga portar för en offentlig IP-adress delas med alla virtuella datorer som är associerade med respektive backend-pool.) Det finns avvägningar att överväga, till exempel den extra kostnaden för offentliga IP-adresser och den potentiella effekten av filtrering av ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för webbarbetarroller.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Använda flera frontends
När du använder offentliga Standard Load Balancer tilldelar du flera [IP-adresser på frontend-sidan](load-balancer-outbound-connections.md) för utgående anslutningar och multiplicerar antalet [tillgängliga SNAT-portar.](load-balancer-outbound-connections.md#preallocatedports)  Skapa en IP-konfiguration, regel och serverpool på serversidan för att utlösa programmering av SNAT till den offentliga IP-adressen för frontend.  Regeln behöver inte fungera och en hälsoavsökning behöver inte lyckas.  Om du även använder flera datorer för inkommande trafik (i stället för bara för utgående), bör du använda anpassade hälsoavsökningar för att säkerställa tillförlitligheten.

>[!NOTE]
>I de flesta fall är uttömning av SNAT-portar ett tecken på felaktig design.  Se till att du förstår varför du tar slut på portar innan du använder fler frontends för att lägga till SNAT-portar.  Du kanske maskerar ett problem som kan leda till fel senare.

## <a name="scale-out"></a><a name="scaleout"></a>Skala ut
[Förallokerade](load-balancer-outbound-connections.md#preallocatedports) portar tilldelas baserat på storleken på backend-poolen och grupperas i nivåer för att minimera störningar när vissa portar måste allokeras om för att hantera nästa större storleksnivå för backend-pool.  Du kan välja att öka SNAT-portanvändningen för en viss frontend genom att skala om din backend-pool till den maximala storleken för en viss nivå.  Kom ihåg att standardporttilldelningen krävs för att programmet ska kunna skala ut effektivt utan risk för SNAT-utmattning.

Två virtuella datorer i serverpoolen skulle till exempel ha 1 024 tillgängliga SNAT-portar per IP-konfiguration, vilket ger totalt 2 048 SNAT-portar för distributionen.  Om distributionen skulle ökas till 50 virtuella datorer, även om antalet förallokerade portar förblir konstanta per virtuell dator, kan totalt 51 200 (50 x 1 024) SNAT-portar användas av distributionen.  Om du vill skala ut distributionen kontrollerar du antalet förallokerade [portar](load-balancer-outbound-connections.md#preallocatedports) per nivå för att se till att du formar utskalning till maxvärdet för respektive nivå.  Om du i föregående exempel hade valt att skala ut till 51 i stället för 50 instanser skulle du gå vidare till nästa nivå och få färre SNAT-portar per virtuell dator samt totalt.

Om du skalar ut till nästa större storleksnivå för backend-poolen finns det risk för att vissa av dina utgående anslutningar får en time out om allokerade portar måste allokeras om.  Om du bara använder några av dina SNAT-portar är utskalning över nästa större storlek på backend-poolen inkonsekvens.  Hälften av de befintliga portarna kommer att allokeras om varje gång du flyttar till nästa nivå i backend-poolen.  Om du inte vill att detta ska ske måste du forma distributionen till nivåstorleken.  Eller se till att programmet kan identifiera och försöka igen efter behov.  TCP-keepalives kan hjälpa till att identifiera när SNAT-portar inte längre fungerar på grund av att de har allokerats om.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Använd keepalives för att återställa tidsgränsen för utgående inaktivitet
Utgående anslutningar har en tidsgräns på 4 minuter för inaktivitet. Den här tidsgränsen kan justeras via [regler för utgående trafik](outbound-rules.md). Du kan också använda transport (till exempel TCP-keepalives) eller keepalives på programlager för att uppdatera ett inaktivt flöde och återställa den här tidsgränsen för inaktivitet om det behövs.  

När du använder TCP keepalives räcker det att aktivera dem på ena sidan av anslutningen. Det räcker till exempel att de endast aktiveras på serversidan för att återställa flödets inaktiva timer och det är inte nödvändigt för båda sidor att initiera TCP-keepalives.  Det finns liknande begrepp för programlager, inklusive konfigurationer för databasklient-server.  Kontrollera på serversidan vilka alternativ som finns för programspecifika keepalives.

## <a name="next-steps"></a>Nästa steg
Vi vill alltid förbättra upplevelsen för våra kunder. Om du har problem med utgående anslutningar som inte listas eller åtgärdas i den här artikeln kan du skicka feedback via GitHub längst ned på den här sidan så tar vi upp din feedback så snart som möjligt.
