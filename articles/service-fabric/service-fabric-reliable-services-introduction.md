---
title: Översikt över programmerings modellen för tillförlitliga tjänster
description: Lär dig mer om Service Fabrics programmerings modell för pålitliga tjänster och kom igång med att skriva dina egna tjänster.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 50b8cae11cef5ee8670e2e0270fd8506b1dad4d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576544"
---
# <a name="reliable-services-overview"></a>Översikt över Reliable Services

Azure Service Fabric fören klar skrivning och hantering av tillstånds lösa och tillstånds känsliga tjänster. I detta avsnitt:

* Reliable Services programmerings modell för tillstånds lösa och tillstånds känsliga tjänster.
* De val du behöver göra när du skriver en tillförlitlig tjänst.
* Några scenarier och exempel på när du ska använda Reliable Services och hur de skrivs.

*Reliable Services* är en av de programmerings modeller som är tillgängliga på Service Fabric. En annan är den *tillförlitliga aktörens* programmerings modell, som tillhandahåller ett ramverk för [virtuella aktörs](https://research.microsoft.com/en-us/projects/orleans/) program ovanpå Reliable Servicess modellen. Mer information om Reliable Actors finns i [Introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric hanterar livs längden för tjänster, från etablering och distribution via uppgradering och borttagning, via [Service Fabric program hantering](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Vad är Reliable Services

Reliable Services ger dig en enkel, kraftfull programmerings modell på översta nivån som hjälper dig att uttrycka vad som är viktigt för ditt program. Med programmerings modellen Reliable Services får du:

* Åtkomst till Service Fabric-API: er. Till skillnad från Service Fabric tjänster som modelleras som [körbara gäst program](service-fabric-guest-executables-introduction.md)kan Reliable Services använda Service Fabric-API: er direkt. Detta gör att tjänster kan:
  * Fråga systemet
  * Rapportera hälsa om entiteter i klustret
  * Ta emot meddelanden om konfigurations-och kod ändringar
  * Hitta och kommunicera med andra tjänster,
  * Använd de [tillförlitliga samlingarna](service-fabric-reliable-services-reliable-collections.md)
  * Få till gång till många andra funktioner från en första klass programmerings modell i flera programmeringsspråk.
* En enkel modell för att köra din egen kod som fungerar som andra välbekanta programmerings modeller. Din kod har en väldefinierad start punkt och enkelt hanterad livs cykel.
* En pluggbar kommunikations modell. Använd den transport du väljer, till exempel HTTP med [webb-API](./service-fabric-reliable-services-communication-aspnetcore.md), WebSockets, anpassade TCP-protokoll eller något annat. Reliable Services innehåller några bra alternativ som du kan använda, eller så kan du ange en egen.
* För tillstånds känsliga tjänster låter Reliable Services programmerings modellen konsekvent och tillförlitligt lagra ditt tillstånd direkt i tjänsten med hjälp av [pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Pålitliga samlingar är en enkel uppsättning med hög tillgängliga samlings klasser som är bekanta för alla som har använt C#-samlingar. Traditionellt förekommande tjänster behövde externa system för tillförlitlig hantering av tillstånd. Med Reliable Collections kan du lagra ditt tillstånd bredvid din beräkning med samma hög tillgänglighet och tillförlitlighet som du kommer att förväntar dig från hög tillgängliga externa butiker. Den här modellen förbättrar också svars tiden eftersom du samplacerar den beräkning och det tillstånd som den behöver för att fungera.

## <a name="what-makes-reliable-services-different"></a>Vad gör Reliable Services olika

Reliable Services skiljer sig från tjänster som du kanske har skrivit tidigare, eftersom Service Fabric tillhandahåller:

* **Tillförlitlighet** – din tjänst hålls även i otillförlitliga miljöer där datorerna havererar eller tar slut på nätverks problem, eller i de fall där tjänsterna själva stöter på fel och kraschar. För tillstånds känsliga tjänster bevaras ditt tillstånd även om det finns ett nätverk eller andra problem.
* **Tillgänglighet** – din tjänst kan kontaktas och svarar. Service Fabric behåller det antal kopior som du vill köra.
* **Skalbarhet** – tjänster är frikopplade från en speciell maskin vara, och de kan växa eller minska efter behov genom att lägga till eller ta bort maskin vara eller andra resurser. Tjänsterna är enkelt partitionerade (särskilt i tillstånds känsliga fall) för att säkerställa att tjänsten kan skala och hantera partiella problem. Tjänster kan skapas och tas bort dynamiskt via kod, vilket gör att fler instanser kan anpassas efter behov, till exempel som svar på kund förfrågningar. Slutligen Service Fabric uppmuntrar tjänster att vara lätta. Service Fabric tillåter att tusentals tjänster tillhandahålls i en och samma process, i stället för att kräva eller dedicera hela OS-instanser eller processer till en enda instans av en tjänst.
* **Konsekvens** – all information som lagras i en tillförlitlig tjänst kan garanteras vara konsekvent. Detta gäller även för flera pålitliga samlingar i en tjänst. Ändringar i samlingar i en tjänst kan göras på ett transaktions effektivt sätt.

## <a name="service-lifecycle"></a>Livs cykel för tjänst

Oavsett om tjänsten är tillstånds känslig eller tillstånds lös Reliable Services tillhandahålla en enkel livs cykel som gör det möjligt att snabbt ansluta din kod och komma igång.  Att komma igång med en ny tjänst kräver att du implementerar två metoder:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – den här metoden är platsen där tjänsten definierar den eller de kommunikations stackar som den vill använda. Kommunikations stacken, till exempel [webb-API](./service-fabric-reliable-services-communication-aspnetcore.md), är det som definierar lyssnings slut punkten eller slut punkterna för tjänsten (hur klienter når tjänsten). Den definierar också hur meddelanden som visas interagerar med resten av Service koden.
* **RunAsync** – den här metoden är den plats där din tjänst kör sin affärs logik och där den skulle starta alla bakgrunds aktiviteter som ska köras under tjänstens livs längd. Den token för avbrutna token som tillhandahålls är en signal för när det här arbetet ska stoppas. Till exempel, om tjänsten behöver hämta meddelanden från en tillförlitlig kö och bearbeta dem, är det här arbetet.

Om du lär dig mer om pålitliga tjänster för första gången läser du på! Om du vill ha en detaljerad genom gång av livs cykeln för tillförlitliga tjänster kan du läsa [Reliable Services Lifecycle-översikten](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exempel tjänster

Låt oss ta en närmare titt på hur Reliable Services modellen fungerar med både tillstånds lösa och tillstånds känsliga tjänster.

### <a name="stateless-reliable-services"></a>Tillstånds lös Reliable Services

En *tillstånds lös tjänst* är en plats där det inte finns någon status i tjänsten över samtal. Alla tillstånd som förekommer är helt värdefulla och kräver inte synkronisering, replikering, persistence eller hög tillgänglighet.

Anta till exempel att du har en kalkylator som inte har minne och som tar emot alla villkor och åtgärder som kan utföras samtidigt.

I det här fallet `RunAsync()` kan (C#) eller `runAsync()` (Java) för tjänsten vara tom eftersom det inte finns någon aktivitets bearbetning i bakgrunden som tjänsten behöver göra. När kalkylator-tjänsten skapas returneras en `ICommunicationListener` (C#) eller `CommunicationListener` (Java) (till exempel [webb-API](./service-fabric-reliable-services-communication-aspnetcore.md)) som öppnar en lyssnande slut punkt på en viss port. Den här lyssnande slut punkten kopplar upp till olika beräknings metoder (exempel: "Add (N1, N2)") som definierar Kalkylatorns offentliga API.

När ett anrop görs från en klient anropas lämplig metod, och kalkylator tjänsten utför åtgärderna för de data som angetts och returnerar resultatet. Ingen status lagras.

Det går inte att lagra något internt tillstånd gör den här exempel beräkningen enkel. Men de flesta tjänster är inte helt tillstånds lösa. I stället Externalize deras tillstånd till någon annan butik. (Till exempel kan alla webbappar som förlitar sig på att bevara sessionstillstånd i en lagrings plats eller cache inte tillstånds lösa.)

Ett vanligt exempel på hur tillstånds lösa tjänster används i Service Fabric är en klient del som visar det offentliga API: t för ett webb program. Frontend-tjänsten pratar sedan med tillstånds känsliga tjänster för att slutföra en användarbegäran. I det här fallet dirigeras anrop från klienter till en känd port, till exempel 80, där den tillstånds lösa tjänsten lyssnar. Den här tillstånds lösa tjänsten tar emot anropet och avgör om anropet kommer från en betrodd part och vilken tjänst den är avsedd för.  Sedan vidarebefordrar den tillstånds lösa tjänsten anropet till rätt partition för den tillstånds känsliga tjänsten och väntar på ett svar. När den tillstånds lösa tjänsten tar emot ett svar svarar den på den ursprungliga klienten. Ett exempel på en sådan tjänst är *Service Fabric komma igång* exemplet ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), bland annat Service Fabric exempel i den lagrings platsen.

### <a name="stateful-reliable-services"></a>Tillstånds känslig Reliable Services

En *tillstånds känslig tjänst* är en tjänst som måste ha en del av tillstånd som hålls konsekvent och finns för att tjänsten ska fungera. Överväg en tjänst som ständigt beräknar ett rullande medelvärde av något värde baserat på de uppdateringar som tas emot. För att göra detta måste den ha den aktuella uppsättningen inkommande begär Anden som den måste bearbeta och det aktuella genomsnittet. Alla tjänster som hämtar, bearbetar och lagrar information i en extern lagrings plats (till exempel en Azure-Blob eller tabell lagring idag) är tillstånds känslig. Det behåller bara sitt tillstånd i det externa tillstånds lagret.

De flesta tjänster lagrar sin status externt, eftersom det externa arkivet är vad som ger tillförlitlighet, tillgänglighet, skalbarhet och konsekvens för det aktuella läget. I Service Fabric behöver inte tjänster lagra sitt tillstånd externt. Service Fabric tar hand om dessa krav för både tjänst koden och tjänstens tillstånd.

Anta att vi vill skriva en tjänst som bearbetar bilder. För att göra detta tar tjänsten i en avbildning och den serie konverteringar som ska utföras på avbildningen. Den här tjänsten returnerar en kommunikations lyssnare (vi antar att det är en WebAPI) som exponerar ett API som `ConvertImage(Image i, IList<Conversion> conversions)` . När den tar emot en begäran, lagrar tjänsten den i a `IReliableQueue` och returnerar ett ID till klienten så att den kan spåra begäran.

I den här tjänsten kan `RunAsync()` vara mer komplex. Tjänsten har en slinga inuti `RunAsync()` som hämtar begär Anden från `IReliableQueue` och utför de begärda konverteringarna. Resultaten lagras i ett `IReliableDictionary` så att när klienten kommer tillbaka kan de få sina konverterade avbildningar. För att säkerställa att även om något Miss lyckas med avbildningen går den här tillförlitliga tjänsten ut ur kön, utför konverteringarna och lagra resultatet i en enskild transaktion. I det här fallet tas meddelandet bort från kön och resultaten lagras bara i resultat ord listan när konverteringen är klar. Alternativt kan tjänsten Hämta avbildningen från kön och omedelbart lagra den i en fjärrlagringsplats. Detta minskar den mängd tillstånd som tjänsten måste hantera, men ökar komplexiteten eftersom tjänsten måste behålla de metadata som krävs för att hantera fjär lagringen. Om något Miss lyckas i mitten av den här metoden fortsätter begäran i kön som väntar på att bearbetas.

Även om den här tjänsten låter som en typisk .NET-tjänst, är skillnaden att de data strukturer som används ( `IReliableQueue` och `IReliableDictionary` ) tillhandahålls av Service Fabric och är mycket pålitliga, tillgängliga och konsekventa.

## <a name="when-to-use-reliable-services-apis"></a>När du ska använda Reliable Services API: er

Överväg Reliable Services API: er om:

* Du vill att tjänstens kod (och eventuellt State) ska vara hög tillgänglig och tillförlitlig.
* Du behöver transaktionella garantier över flera status enheter (till exempel order och order rads objekt).
* Programmets tillstånd kan modelleras naturligt som pålitliga ord listor och köer.
* Din program kod eller tillstånd måste ha hög tillgänglighet med läsningar och skrivningar med låg latens.
* Ditt program måste kontrol lera samtidigheten eller granularitet för överförda åtgärder i en eller flera pålitliga samlingar.
* Du vill hantera kommunikationen eller kontrol lera partitionerings schemat för din tjänst.
* Din kod behöver en kostnads fri tråd körnings miljö.
* Ditt program måste skapa eller förstöra pålitliga ord listor eller köer eller hela tjänster vid körning.
* Du måste kontrol lera program mässigt Service Fabric säkerhets kopierings-och återställnings funktioner i tjänstens tillstånd.
* Ditt program måste underhålla ändrings historiken för dess status enheter.
* Du vill utveckla eller använda tredjeparts-utvecklade, anpassade tillstånds leverantörer.

## <a name="next-steps"></a>Nästa steg

* [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
* [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
* [Programmerings modellen för Reliable Actors](service-fabric-reliable-actors-introduction.md)
