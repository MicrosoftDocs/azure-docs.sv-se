---
title: Program scenarier och design
description: Översikt över kategorier av moln program i Service Fabric. Diskuterar program design som använder tillstånds känsliga och tillstånds lösa tjänster.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 6c3cc931a85b91fc02b8086ca5c2481153691e54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575677"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric program scenarier

Azure Service Fabric erbjuder en tillförlitlig och flexibel plattform där du kan skriva och köra många typer av affärs program och tjänster. Dessa program och mikrotjänster kan vara tillstånds lösa eller tillstånds känsliga och de är resurs Utjämnings bara på virtuella datorer för att maximera effektiviteten.

Den unika arkitekturen i Service Fabric gör det möjligt att utföra data analyser i nära real tid, i minnes beräkning, parallella transaktioner och händelse bearbetning i dina program. Du kan enkelt skala dina program i eller ut beroende på vilka resurs krav som ändras.

Design rikt linjer för att skapa program finns [i arkitekturen för mikrotjänster på Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) och [metod tips för program design med hjälp av Service Fabric](service-fabric-best-practices-applications.md).

Överväg att använda Service Fabric plattform för följande typer av program:

* **Data insamling, bearbetning och IoT**: Service Fabric hanterar stor skala och har låg latens via tillstånds känsliga tjänster. Den kan hjälpa till att bearbeta data på miljon tals enheter där data för enheten och beräkningen är samplacerade.

    Kunder som har skapat IoT-tjänster med hjälp av Service Fabric inkluderar [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL-konstruktion](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure),  [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider elektriska](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)och nät verks [system](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Interaktiva program för spel och sessioner**: Service Fabric är användbart om ditt program kräver läsningar och skrivningar med låg latens, t. ex. i online-spel eller Instant Messaging. Med Service Fabric kan du skapa dessa interaktiva och tillstånds känsliga program utan att behöva skapa en separat lagrings plats eller cache. Besök [Azures spel lösningar](https://azure.microsoft.com/solutions/gaming/) för att få vägledning om hur du [använder Service Fabric i spel tjänster](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Kunder som har skapat spel tjänster omfattar [Nästa spel](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) och [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Kunder som har skapat interaktiva sessioner inkluderar [Honeywell med HoloLens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Data analys och arbets flödes bearbetning**: program som måste bearbeta händelser eller data strömmar med data förmånen från optimerade läsningar och skrivningar i Service Fabric. Service Fabric stöder även pipeliner för program bearbetning, där resultaten måste vara pålitliga och skickas vidare till nästa bearbetnings steg utan att förloras. Dessa pipeliner omfattar transaktions-och finans system, där data konsekvens och beräknings garantier är viktiga.

    Kunder som har skapat affärs arbets flödes tjänster inkluderar [Zeiss-grupp](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [kvorum affärs lösningar](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)och [Société allmänt](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Beräkning av data**: Service Fabric gör att du kan skapa tillstånds känsliga program som utför intensiv data beräkning. Service Fabric tillåter samplaceringen av bearbetning (beräkning) och data i program. 

   Normalt när ditt program kräver åtkomst till data, begränsar nätverks svars tiden som är associerad med en extern datacache eller lagrings nivå beräknings tiden. Tillstånds känsliga Service Fabric tjänster eliminerar den svars tiden, vilket möjliggör mer optimerade läsningar och skrivningar.

   Anta till exempel ett program som utför rekommendationer i nära real tid för kunder, med ett tids krav för fördröjning på mindre än 100 millisekunder. Svars tiderna och prestanda egenskaperna för Service Fabric Services ger en svars upplevelse för användaren, jämfört med standard implementerings modellen för att hämta nödvändiga data från Fjärrlagring. Systemet svarar bättre eftersom beräkningen av rekommendations valet samplaceras med data och regler.

    Kunder som har skapat beräknings tjänster omfattar [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) och [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Tjänster med hög tillgänglighet**: Service Fabric ger snabb redundans genom att skapa flera sekundära tjänst repliker. Om en nod, en process eller en enskild tjänst slutar fungera på grund av maskin vara eller andra problem, uppgraderas en av de sekundära replikerna till en primär replik med minimal förlust av tjänst.

* **Skalbara tjänster**: enskilda tjänster kan partitioneras, så att tillstånd kan skalas ut över klustret. Enskilda tjänster kan också skapas och tas bort i farten. Du kan skala ut tjänster från några få instanser på flera noder till tusentals instanser på många noder och sedan skala dem igen om det behövs. Du kan använda Service Fabric för att bygga dessa tjänster och hantera hela livs cykeln.

## <a name="application-design-case-studies"></a>Fallstudier för program design

Fallstudier som visar hur Service Fabric används för att utforma program publiceras i [kundernas berättelser](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) och [mikrotjänster på Azure](https://azure.microsoft.com/solutions/microservice-applications/) -platser.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Utforma program som består av tillstånds lösa och tillstånds känsliga mikrotjänster

Att skapa program med Azure Cloud Services Worker-roller är ett exempel på en tillstånds lös tjänst. Tillstånds känsliga mikrotjänster upprätthåller däremot sitt auktoritativa tillstånd bortom begäran och dess svar. Den här funktionen ger hög tillgänglighet och konsekvens för tillstånd via enkla API: er som tillhandahåller transaktionella garantier som backas upp av replikeringen.

Tillstånds känsliga tjänster i Service Fabric ger hög tillgänglighet till alla typer av program, inte bara databaser och andra data lager. Detta är en naturlig förloppet. Program har redan flyttats från att använda rent Relations databaser för hög tillgänglighet till NoSQL-databaser. Programmen kan nu ha sina "varma" tillstånd och data som hanteras i dem för ytterligare prestanda vinster utan att kompromissa med tillförlitlighet, konsekvens eller tillgänglighet.

När du skapar program som består av mikrotjänster har du vanligt vis en kombination av tillstånds lösa webbappar (t. ex. ASP.NET och Node.js) som anropar tillstånds lösa och tillstånds känsliga tjänster mellan nivåer. Appar och tjänster distribueras i samma Service Fabric-kluster via Service Fabric distributions kommandon. Var och en av dessa tjänster är oberoende av hänsyn till skalning, tillförlitlighet och Resursanvändning. Detta oberoende förbättrar flexibiliteten och flexibiliteten vid utveckling och livs cykel hantering.

Tillstånds känsliga mikrotjänster fören klar programdesignerna eftersom de tar bort behovet av de ytterligare köer och cacheminnen som traditionellt har varit nödvändiga för att hantera tillgänglighets-och latens kraven för rent tillstånds lösa program. Eftersom tillstånds känsliga tjänster har hög tillgänglighet och låg latens, finns det färre detaljer att hantera i ditt program.

Följande diagram illustrerar skillnaderna mellan att utforma ett program som är tillstånds löst och ett tillstånds känsligt. Genom att dra nytta av [Reliable Services](service-fabric-reliable-services-introduction.md) och [Reliable Actors](service-fabric-reliable-actors-introduction.md) programmerings modeller, minskar tillstånds känsliga tjänster program komplexiteten och ger stora data flöden och låg latens.

Här är ett exempel program som använder tillstånds lösa tjänster: ![ program som använder tillstånds lösa tjänster][Image1]

Här är ett exempel program som använder tillstånds känsliga tjänster: ![ program som använder tillstånds känsliga tjänster][Image2]

## <a name="next-steps"></a>Nästa steg

* Kom igång med att skapa tillstånds lösa och tillstånds känsliga tjänster med Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) och [Reliable Actors](service-fabric-reliable-actors-get-started.md) programmerings modeller.
* Besök Azure Architecture Center för att få hjälp med att [skapa mikrotjänster på Azure](/azure/architecture/microservices/).
* Gå till [Azure Service Fabric program och kluster metod tips](service-fabric-best-practices-overview.md) för vägledning för program design.

* Se även:
  * [Förstå mikrotjänster](service-fabric-overview-microservices.md)
  * [Definiera och hantera tjänst tillstånd](service-fabric-concepts-state.md)
  * [Tillgänglighet för tjänster](service-fabric-availability-services.md)
  * [Skala tjänster](service-fabric-concepts-scalability.md)
  * [Partition tjänster](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
