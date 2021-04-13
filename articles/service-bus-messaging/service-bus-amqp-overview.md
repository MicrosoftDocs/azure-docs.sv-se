---
title: Översikt över AMQP 1,0 i Azure Service Bus
description: Lär dig hur Azure Service Bus stöder Advanced Message Queueing Protocol (AMQP), ett öppet standard protokoll.
ms.topic: article
ms.date: 04/08/2021
ms.openlocfilehash: 006511789bfa93f8e7d578ed21a73a2563fb4c6b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304430"
---
# <a name="advanced-message-queueing-protocol-amqp-10-support-in-service-bus"></a>Advanced Message Queueing Protocol (AMQP) 1,0-stöd i Service Bus
Azure Service Bus moln tjänsten använder [AMQP 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) som primär kommunikations metod. Microsoft har samarbetat med partner över hela branschen, både kunder och leverantörer av konkurrerande meddelande hanterare, för att utveckla och utveckla AMQP under de senaste tio åren, med nya tillägg som utvecklas i [Oasis AMQP tekniska kommittén](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 är en ISO-och IEC standard ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP gör att du kan skapa plattforms oberoende hybrid program med ett icke-neutralt och implementerat, standard protokoll. Du kan skapa program med hjälp av komponenter som har skapats med olika språk och ramverk och som körs på olika operativ system. Alla dessa komponenter kan ansluta till Service Bus och sömlöst utbyta strukturerade affärs meddelanden effektivt och med full kvalitet.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduktion: Vad är AMQP 1,0 och varför är det viktigt?
Traditionellt förekommande, meddelanden mellan olika program varor har använt egna protokoll för kommunikation mellan klient program och-utjämnare. Det innebär att när du har valt en viss leverantörs meddelande Broker måste du använda leverantörens bibliotek för att ansluta dina klient program till denna Broker. Det resulterar i en grad av beroende av den leverantören, eftersom det krävs kod ändringar i alla anslutna program för att transportera ett program till en annan produkt. I Java-communityn har språkspecifika API-standarder som Java Message Service (JMS) och vårens sammanställningar förkortat så lite, men har en begränsad funktions omfattning och undantar utvecklare från andra språk.

Dessutom är det svårt att ansluta meddelande utjämnare från olika leverantörer. Det kräver vanligt vis att program nivå bryggningen flyttar meddelanden från ett system till ett annat och översätter mellan deras egna meddelande format. Det är ett vanligt krav. till exempel när du måste ange ett nytt enhetligt gränssnitt till äldre system, eller integrera IT-system efter en sammanslagning. AMQP tillåter anslutning av anslutnings utjämning direkt, till exempel genom att använda routrar som [Apache qpid Dispatch router](https://qpid.apache.org/components/dispatch-router/index.html) eller Broker-Native "shovels" som en av [rabbitmq](service-bus-integrate-with-rabbitmq.md).

Program varu branschen är en snabb och rörlig verksamhet. nya programmeringsspråk och program ramverk introduceras ibland bewildering takt. På samma sätt utvecklas kraven i IT-systemen över tid och utvecklare vill dra nytta av de senaste plattforms funktionerna. Men ibland stöder inte den valda meddelande leverantören dessa plattformar. Om meddelande protokollen är tillverkarspecifika är det inte möjligt för andra att tillhandahålla bibliotek för dessa nya plattformar. Därför måste du använda metoder som att skapa gatewayer eller bryggor som gör att du kan fortsätta att använda meddelande produkten.

Utvecklingen av Advanced Message Queueing Protocol (AMQP) 1,0 motiverades av dessa problem. Den har sitt ursprung på JP-Morgan, som, till exempel de flesta finansiella tjänste företag, är stora användare av meddelanderoutning mellanprodukter. Målet var enkelt: om du vill skapa ett meddelande protokoll med öppen standard som gjorde det möjligt att skapa meddelandebaserade program med hjälp av komponenter som skapats med olika språk, ramverk och operativ system kan du använda de bästa komponenterna från ett utbud av leverantörer.

## <a name="amqp-10-technical-features"></a>AMQP 1,0-tekniska funktioner
AMQP 1,0 är ett effektivt, tillförlitligt meddelande protokoll på trådnivå som du kan använda för att bygga robusta, plattforms oberoende meddelande program. Protokollet har ett enkelt mål: för att definiera Mechanics för säker, tillförlitlig och effektiv överföring av meddelanden mellan två parter. Själva meddelandena kodas med hjälp av en bärbar data representation som gör det möjligt för heterogena avsändare och mottagare att utbyta strukturerade företags meddelanden med full åter givning. Här är en sammanfattning av de viktigaste funktionerna:

* **Effektiv**: AMQP 1,0 är ett anslutnings orienterat protokoll som använder en binär kodning för protokoll instruktionerna och de affärs meddelanden som överförs via det. Den införlivar avancerade flödes kontroll scheman för att maximera användningen av nätverket och de anslutna komponenterna. I detta fall har protokollet utformats för att ge en balans mellan effektivitet, flexibilitet och interoperabilitet.
* **Tillförlitligt**: AMQP 1,0-protokollet gör att meddelanden kan bytas ut med en mängd Tillförlitlighets garantier, från Fire-och-glömma till tillförlitlig, exakt när leveransen har godkänts.
* **Flexibel**: AMQP 1,0 är ett flexibelt protokoll som kan användas för att stödja olika topologier. Samma protokoll kan användas för klient-till-klient-, klient-till-Broker-och Broker-till-Broker-kommunikation.
* **Broker-Model-oberoende**: AMQP 1,0-specifikationen gör inga krav på den meddelande modell som används av en Broker. Det innebär att det är möjligt att enkelt lägga till AMQP 1,0-stöd för befintliga meddelande hanterare.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 är en standard (med versaler ')
AMQP 1,0 är en internationell standard som godkänts av ISO och IEC som ISO/IEC 19464:2014.

AMQP 1,0 har varit i utvecklings läge sedan 2008 av en kärn grupp med fler än 20 företag, både teknik leverantörer och slutanvändarens företag. Under den tiden har användare företag bidragit med sina verkliga affärs krav och teknik leverantörer har utvecklat protokollet för att uppfylla dessa krav. Under hela processen har leverantörer deltagit i verkstäder där de samarbetar för att verifiera samverkan mellan sina implementeringar.

I oktober 2011 släpptes utvecklings arbetet över till en teknisk kommitté inom organisationen för förhands OASIS (Structured Information Standards) och OASIS AMQP 1,0 standard i oktober 2012. Följande företag deltog i den tekniska kommittén vid utvecklingen av standarden:

* **Teknik leverantörer**: Axway program vara, Huawei Technologies, IIT-programvara, INETCO-system, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, process program, Red Hat, Sita, program-AG, Solace-system, VMware, WSO2, Zenika.
* **Användar företag**: bank i Amerika, kredit Suisse, tyska Boerse, Goldman Sachs, JPMorgan

De aktuella stolarna i [Oasis AMQP tekniska kommittén](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) representerar Red Hat och Microsoft.

Några av de mest citerade fördelarna med öppna standarder är:

* Mindre chans att låsa leverantören
* Samverkan
* Bred tillgänglighet för bibliotek och verktyg
* Skydd mot Obsolescence
* Tillgänglighet för kunnig personal
* Lägre och hanterbar risk

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 och Service Bus
AMQP 1,0-stöd i Azure Service Bus innebär att du kan använda hanterings funktionerna Service Bus köer och publicera/prenumerera på asynkrona meddelande funktioner från en uppsättning plattformar med hjälp av ett effektivt binärt protokoll. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativ system.

Följande bild illustrerar en exempel distribution där Java-klienter som körs på Linux, skrivet med hjälp av JMS-API: et (standard Java Message Service) och .NET-klienter som körs på Windows, Exchange-meddelanden via Service Bus med AMQP 1,0.

![Diagram som visar en Service Bus att utbyta meddelanden med två Linux-miljöer och två Windows-miljöer.][0]

**Bild 1: exempel på distributions scenario som visar meddelanden över plattformar med hjälp av Service Bus och AMQP 1,0**

Alla tillgängliga Service Bus klient bibliotek som stöds via Azure SDK använder AMQP 1,0.

- [Azure Service Bus för .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus bibliotek för Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus Provider för Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Azure Service Bus moduler för Java Script och TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus bibliotek för python](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Dessutom kan du använda Service Bus från valfri AMQP 1,0-kompatibel protokolls tack:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Sammanfattning
* AMQP 1,0 är ett öppet, Reliable meddelande protokoll som du kan använda för att skapa hybrid program mellan plattformar. AMQP 1,0 är en OASIS-standard.

## <a name="next-steps"></a>Nästa steg
Vill du lära dig mer? Besök följande länkar:

* [Använda Service Bus från .NET med AMQP]
* [Använda Service Bus från Java med AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Använda Service Bus från .NET med AMQP]: service-bus-amqp-dotnet.md
[Använda Service Bus från Java med AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md

