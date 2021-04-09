---
title: Lägg till anpassade Service Fabric hälso rapporter
description: Beskriver hur du skickar anpassade hälso rapporter till Azure Service Fabric hälsoentiteter. Ger rekommendationer för att utforma och implementera kvalitets hälso rapporter.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c87c063ac9d3b4810657f72c46c17725b8899c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626177"
---
# <a name="add-custom-service-fabric-health-reports"></a>Lägg till anpassade Service Fabric hälso rapporter
Azure Service Fabric introducerar en [hälso modell](service-fabric-health-introduction.md) som har utformats för att flagga kluster och program villkor på vissa enheter. Hälso modellen använder **hälso rapporter** (system komponenter och övervaknings rapporter). Målet är enkelt och snabbt att diagnostisera och reparera. Service Writers måste vara på väg om hälso tillståndet. Alla villkor som kan påverka hälsan bör rapporteras, särskilt om det kan hjälpa till att flagga problem nära roten. Hälso informationen kan spara tid och ansträngning för fel sökning och undersökning. Användbarheten är särskilt tydlig när tjänsten är igång och körs i molnet (privat eller Azure).

Service Fabric rapporteraren övervakar identifierade villkor för intresse. De rapporterar om dessa villkor baserat på deras lokala vy. [Hälso arkivet](service-fabric-health-introduction.md#health-store) sammanställer hälso data som skickas av alla rapporter för att avgöra om entiteterna är globalt felfria. Modellen är avsedd att vara omfattande, flexibel och lätt att använda. Hälso rapportens kvalitet bestämmer noggrannheten för klustrets hälso tillstånd. Falska positiva identifieringar som felaktigt visar fel som inte är felfria kan påverka uppgraderingar eller andra tjänster som använder hälso data. Exempel på sådana tjänster är reparations tjänster och aviserings metoder. Därför behövs det en del tanke på att ge rapporter som upprättar förutsättningar på bästa möjliga sätt.

För att utforma och implementera hälso rapporter måste övervaknings enheten och system komponenterna:

* Definiera det villkor som de är intresserade av, hur de övervakas och inverkan på klustret eller programmets funktioner. Utifrån den här informationen bestämmer du hälso rapportens egenskaper och hälso tillståndet.
* Bestäm vilken [entitet](service-fabric-health-introduction.md#health-entities-and-hierarchy) rapporten gäller för.
* Ta reda på var rapportering görs, inifrån tjänsten eller från en intern eller extern övervaknings enhet.
* Definiera en källa som används för att identifiera rapportören.
* Välj en rapporterings strategi, antingen regelbundet eller via över gångar. Det rekommenderade sättet är att regelbundet, eftersom det kräver enklare kod och är mindre känsligt för fel.
* Ta reda på hur länge rapporten om felaktiga tillstånd ska ligga kvar i hälso lagret och hur den ska tas bort. Med hjälp av den här informationen bestämmer du rapportens tid för Live och ta bort på förfallo sätt.

Som nämnts kan rapportering göras från:

* Den övervakade Service Fabric tjänst repliken.
* Interna övervaknings regler distribueras som en Service Fabric tjänst (till exempel en Service Fabric tillstånds lös tjänst som övervakar villkor och ärende rapporter). Övervaknings enheten kan distribueras till alla noder eller kan tillhör till den övervakade tjänsten.
* Interna övervaknings grupper som körs på Service Fabric noder, men som *inte* implementeras som Service Fabric-tjänster.
* Externa övervaknings grupper som avsöker resursen från en plats *utanför* service Fabrics klustret (till exempel övervaknings tjänsten som Gomez).

> [!NOTE]
> I rutan är klustret ifylld med hälso rapporter som skickas av system komponenterna. Läs mer i [använda system hälso rapporter för fel sökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Användar rapporterna måste skickas på [hälsoentiteter](service-fabric-health-introduction.md#health-entities-and-hierarchy) som redan har skapats av systemet.
> 
> 

När hälso rapport designen är klar kan hälso rapporter skickas enkelt. Du kan använda [FabricClient](/dotnet/api/system.fabric.fabricclient) för att rapportera hälso tillstånd om klustret inte är [säkert](service-fabric-cluster-security.md) eller om Fabric-klienten har administratörs behörighet. Rapportering kan göras via API: et genom att använda [FabricClient. HealthManager. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), via PowerShell eller genom rest. Konfigurations rattar batch rapporter för bättre prestanda.

> [!NOTE]
> Rapport hälsan är synkron och representerar bara verifierings arbetet på klient sidan. Det faktum att rapporten accepteras av hälso klienten eller att- `Partition` eller- `CodePackageActivationContext` objekten inte innebär att den används i butiken. Den skickas asynkront och kan eventuellt grupperas med andra rapporter. Bearbetningen på servern kan fortfarande Miss lyckas: sekvensnumret kan vara inaktuellt, den entitet som rapporten ska tillämpas på har tagits bort, osv.
> 
> 

## <a name="health-client"></a>Hälso klient
Hälso rapporterna skickas till hälso tillstånds hanteraren via en hälso klient som finns i Fabric-klienten. Hälso hanteraren sparar rapporter i hälso lagret. Hälso klienten kan konfigureras med följande inställningar:

* **HealthReportSendInterval**: fördröjningen mellan den tid då rapporten läggs till i klienten och den tidpunkt då den skickas till Health Manager. Används för att gruppera rapporter i ett enda meddelande i stället för att skicka ett meddelande för varje rapport. Batchen förbättrar prestandan. Standard: 30 sekunder.
* **HealthReportRetrySendInterval**: intervallet som hälso klienten skickar om ackumulerade hälso rapporter till Health Manager. Standard: 30 sekunder, minst: 1 sekund.
* **HealthOperationTimeout**: tids gränsen för ett rapport meddelande som skickas till hälso tillstånds hanteraren. Om ett meddelande har nått sin tids gräns, försöker hälso klienten att försöka igen tills hälso chefen bekräftar att rapporten har bearbetats. Standard: två minuter.

> [!NOTE]
> När rapporterna är grupperade måste Fabric-klienten hållas aktiv i minst HealthReportSendInterval för att säkerställa att de skickas. Om meddelandet förloras eller hälso hanteraren inte kan tillämpa dem på grund av tillfälliga fel, måste Fabric-klienten hållas igång längre för att det ska gå att försöka igen.
> 
> 

Buffringen på klienten tar hänsyn till att rapporterna är unika. Om till exempel en viss felaktig rapportering rapporterar 100 rapporter per sekund på samma egenskap för samma entitet, ersätts rapporterna med den senaste versionen. Högst en sådan rapport finns i klient kön. Om batchbearbetning har kon figurer ATS är antalet rapporter som skickas till Health Manager bara ett per sändnings intervall. Den här rapporten är den senast tillagda rapporten, som visar det mest aktuella status för entiteten.
Ange konfigurations parametrar när `FabricClient` har skapats genom att skicka [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) med de önskade värdena för hälso relaterade poster.

I följande exempel skapas en Fabric-klient och anger att rapporterna ska skickas när de läggs till. Vid timeout och fel som kan göras igen sker nya försök var 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Vi rekommenderar att du behåller standard klient inställningarna för infrastruktur resurser, som är `HealthReportSendInterval` 30 sekunder. Den här inställningen säkerställer optimala prestanda på grund av batchering. För kritiska rapporter som måste skickas så snart som möjligt använder du `HealthReportSendOptions` med omedelbar `true` i [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Omedelbara rapporter kringgår batch-intervallet. Använd den här flaggan i försiktighet; Vi vill dra nytta av hälso klientens batching närhelst det är möjligt. Omedelbar sändning är också användbart när Fabric-klienten stängs (till exempel om processen har avgjort ett ogiltigt tillstånd och måste stängas för att förhindra sido effekter). Den ser till att det är en god ansträngnings överföring av de ackumulerade rapporterna. När en rapport läggs till med en omedelbar flagga, slår hälso klienten ihop alla ackumulerade rapporter sedan den senaste sändningen.

Samma parametrar kan anges när en anslutning till ett kluster skapas via PowerShell. I följande exempel startas en anslutning till ett lokalt kluster:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

På samma sätt som API kan rapporter skickas med hjälp av `-Immediate` växeln som skickas omedelbart, oavsett `HealthReportSendInterval` värdet.

För REST skickas rapporterna till Service Fabric Gateway, som har en intern Fabric-klient. Den här klienten är som standard konfigurerad för att skicka rapporter med en gång i 30 sekunder. Du kan ändra batch-intervallet med kluster konfigurations inställningen `HttpGatewayHealthReportSendInterval` på `HttpGateway` . Som vi nämnt är ett bättre alternativ att skicka rapporterna med `Immediate` true. 

> [!NOTE]
> För att säkerställa att obehöriga tjänster inte kan rapportera hälso tillstånd mot entiteterna i klustret konfigurerar du servern så att den endast accepterar begär Anden från skyddade klienter. Den `FabricClient` som används för rapportering måste ha aktive rad säkerhet för att kunna kommunicera med klustret (till exempel med Kerberos eller certifikatautentisering). Läs mer om [kluster säkerhet](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapportera inifrån tjänster med låg behörighet
Om Service Fabric Services inte har administratörs åtkomst till klustret kan du rapportera hälso tillståndet för entiteter från den aktuella kontexten via `Partition` eller `CodePackageActivationContext` .

* För tillstånds lösa tjänster använder du [IStatelessServicePartition. ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) för att rapportera om den aktuella tjänst instansen.
* För tillstånds känsliga tjänster använder du [IStatefulServicePartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) för att rapportera om den aktuella repliken.
* Använd [IServicePartition. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) för att rapportera om den aktuella partitionen.
* Använd [CodePackageActivationContext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) för att rapportera om det aktuella programmet.
* Använd [CodePackageActivationContext. ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) för att rapportera om det aktuella program som distribuerats på den aktuella noden.
* Använd [CodePackageActivationContext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) för att rapportera om ett tjänst paket för det program som distribueras på den aktuella noden.

> [!NOTE]
> Internt, `Partition` och `CodePackageActivationContext` innehåller en hälso klient som kon figurer ATS med standardinställningar. Som förklaras för [hälso klienten](service-fabric-report-health.md#health-client), grupperas och skickas rapporterna på en timer. Objekten bör hållas aktiva för att få möjlighet att skicka rapporten.
> 
> 

Du kan ange `HealthReportSendOptions` när du skickar rapporter via `Partition` och `CodePackageActivationContext` hälso-API: er. Om du har viktiga rapporter som måste skickas så snart som möjligt, använder du `HealthReportSendOptions` med omedelbar `true` . Omedelbara rapporter kringgår batch-intervallet för den interna hälso klienten. Som nämnts tidigare använder du den här flaggan med försiktighet; Vi vill dra nytta av hälso klientens batching närhelst det är möjligt.

## <a name="design-health-reporting"></a>Design hälso rapportering
Det första steget när du genererar rapporter med hög kvalitet identifierar de villkor som kan påverka tjänstens hälsa. Ett villkor som kan hjälpa till att flagga problem i tjänsten eller klustret när det startar, eller till och med bättre, innan ett problem uppstår – kan eventuellt spara miljard tals dollar. Fördelarna är mindre drift tid, färre natt timmar som ägnats åt att undersöka och reparera problem och högre kund nöjdhet.

När villkoren har identifierats behöver övervaknings resurserna för att ta reda på det bästa sättet att övervaka dem för balans mellan omkostnader och användbarhet. Anta till exempel en tjänst som utför komplexa beräkningar som använder vissa temporära filer på en resurs. En övervaknings enhet kan övervaka resursen för att säkerställa att tillräckligt med utrymme är tillgängligt. Det kan lyssna efter meddelanden om fil-eller katalog ändringar. En varning kan rapporteras om ett tröskelvärde uppnås och ett fel rapporteras om resursen är full. Vid en varning kan ett reparations system börja rensa äldre filer på resursen. Vid ett fel kan ett reparations system flytta tjänst repliken till en annan nod. Observera hur villkors tillstånden beskrivs i hälso tillståndet: tillståndet för villkoret som kan betraktas som felfritt (OK) eller som inte är felfritt (varning eller fel).

När du har angett övervaknings informationen måste en övervaknings övervaknings skrivare ta reda på hur du implementerar övervaknings enheten. Om villkoren kan fastställas inifrån tjänsten kan övervaknings enheten vara en del av själva den övervakade tjänsten. Service koden kan till exempel kontrol lera resurs användningen och sedan rapportera varje gång den försöker skriva en fil. Fördelen med den här metoden är att rapportering är enkelt. Försiktighet måste vidtas för att förhindra att övervaknings felen påverkar tjänst funktionerna.

Rapportering inifrån den övervakade tjänsten är inte alltid ett alternativ. En övervaknings enhet i tjänsten kanske inte kan identifiera villkoren. Det kanske inte finns logiken eller data för att bestämma. Behovet av övervakning av villkoren kan vara högt. Villkoren kan inte heller vara speciella för en tjänst, utan påverkar i stället interaktioner mellan tjänster. Ett annat alternativ är att ha en övervaknings enhet i klustret som separata processer. Övervaknings enheten övervakar villkoren och rapporten utan att påverka huvud tjänsterna på något sätt. De här övervaknings grupperna kan till exempel implementeras som tillstånds lösa tjänster i samma program, distribueras på alla noder eller på samma noder som tjänsten.

Ibland är en övervaknings enhet som körs i klustret inte något alternativ. Om det övervakade villkoret är tjänstens tillgänglighet eller funktion när användarna ser det, är det bäst att ha övervaknings enheten på samma plats som användar klienterna. De kan testa åtgärderna på samma sätt som användarna anropar dem. Du kan till exempel ha en övervaknings enhet som ligger utanför klustret, utfärdar förfrågningar till tjänsten och kontrol lera svars tiden och korrekthet för resultatet. (För en beräknings tjänst har exempelvis 2 + 2 retur 4 på en rimlig tid?)

När informationen om övervaknings enheten har slutförts bör du bestämma ett käll-ID som identifierar den unikt. Om flera övervaknings grupper av samma typ är boende i klustret, måste de rapporteras om olika entiteter eller, om de rapporterar till samma entitet, använda olika käll-ID eller egenskap. På så sätt kan deras rapporter finnas. Hälso rapportens egenskap ska avbilda det övervakade villkoret. (I exemplet ovan kan egenskapen vara **ShareSize**.) Om flera rapporter gäller samma villkor, ska egenskapen innehålla viss dynamisk information som gör det möjligt för rapporter att fungera. Om flera resurser till exempel behöver övervakas kan egenskaps namnet vara **ShareSize-ShareName**.

> [!NOTE]
> Använd *inte* hälso lagret för att behålla statusinformation. Endast hälsorelaterad information bör rapporteras som hälso tillstånd, eftersom denna information påverkar hälso utvärderingen av en enhet. Hälso lagret har inte utformats som en allmän lagrings plats. Den använder hälso utvärderings logiken för att samla in alla data i hälso tillståndet. Att skicka information som inte är relaterad till hälsan (som rapporterings status med hälso tillståndet OK) påverkar inte det sammanlagda hälso tillståndet, men det kan påverka hälso lagrets prestanda negativt.
> 
> 

Nästa besluts punkt är den entitet som rapporten ska rapporteras om. I de flesta fall identifierar villkoret entiteten tydligt. Välj entiteten med bästa möjliga granularitet. Om ett villkor påverkar alla repliker i en partition, rapporten på partitionen, inte på tjänsten. Det finns hörn fall där mer information krävs, men. Om villkoret påverkar en entitet, t. ex. en replik, men vill att villkoret ska flaggas under mer än varaktigheten för replikens varaktighet, ska den rapporteras på partitionen. Annars, när repliken tas bort, rensar hälso arkivet alla sina rapporter. Övervaknings enhets skrivare måste tänka på enhetens livs längd och rapporten. Det måste vara klart när en rapport ska rensas från en butik (till exempel när ett fel som rapporteras i en entitet inte längre gäller).

Nu ska vi titta på ett exempel som sätter samman de punkter som jag har beskrivet. Överväg ett Service Fabric-program som består av en Master stateable-beständig tjänst och sekundära tillstånds lösa tjänster som distribueras på alla noder (en sekundär tjänst typ för varje typ av aktivitet). Befälhavaren har en bearbetnings kö som innehåller kommandon som ska köras av sekundär servrar. Sekundärerna kör inkommande begär Anden och skickar bekräftelse signaler. Ett villkor som kan övervakas är längden på huvud bearbetnings kön. Om längden på huvud kön når ett tröskelvärde rapporteras en varning. Varningen anger att sekundärerna inte kan hantera belastningen. Om kön når den maximala längden och kommandon tas bort, rapporteras ett fel eftersom tjänsten inte kan återställas. Rapporterna kan finnas i egenskapen **QueueStatus**. Övervaknings enheten finns inuti tjänsten och skickas regelbundet på huvud repliken. Tiden till Live är två minuter och skickas regelbundet var 30: e sekund. Om den primära slutar fungera rensas rapporten automatiskt från Store. Om tjänst repliken är igång, men den är i död läge eller har andra problem, förfaller rapporten i hälso lagret. I det här fallet utvärderas entiteten vid fel.

Ett annat villkor som kan övervakas är körnings tid för aktiviteten. Master distribuerar uppgifter till sekundärerna baserat på uppgifts typen. Beroende på designen kan befälhavaren polla sekundärerna för uppgifts status. Det kan också vänta på att sekundär servrar skickar tillbaka bekräftelse signaler när de är klara. I det andra fallet måste försiktighet vidtas för att identifiera situationer där sekundära tärningar eller meddelanden går förlorade. Ett alternativ är att huvud servern skickar en ping-begäran till samma sekundära, som skickar tillbaka dess status. Om ingen status tas emot, ser originalet det som ett fel och schemalägger om uppgiften. Detta förutsätter att aktiviteterna är idempotenta.

Det övervakade villkoret kan översättas som en varning om uppgiften inte utförs inom en viss tid (**T1**, till exempel 10 minuter). Om aktiviteten inte slutförs i tid (**T2**, till exempel 20 minuter) kan det övervakade villkoret översättas som fel. Den här rapporten kan göras på flera sätt:

* Den primära huvud repliken rapporterar regelbundet. Du kan ha en egenskap för alla väntande uppgifter i kön. Om minst en aktivitet tar längre tid, är rapport statusen för egenskapen **PendingTasks** en varning eller ett fel, efter vad som är tillämpligt. Om det inte finns några väntande uppgifter eller om alla aktiviteter har startats, är rapportens status OK. Uppgifterna är permanenta. Om den primära servern går nedåt kan den nyligen upphöjda primären fortsätta att rapportera korrekt.
* En annan övervaknings process (i molnet eller externa) kontrollerar aktiviteterna (från utsidan, baserat på det önskade aktivitets resultatet) för att se om de är slutförda. Om de inte respekterar tröskelvärdena skickas en rapport till huvud tjänsten. En rapport skickas också till varje aktivitet som innehåller aktivitets-ID, t. ex. **PendingTask + taskId**. Rapporter bör endast skickas på fel tillstånd. Ställ in tiden på ett par minuter och markera de rapporter som ska tas bort när de går ut för att säkerställa rensning.
* Den sekundära som kör en aktivitets rapport när det tar längre tid än förväntat att köra den. Den rapporterar om tjänst instansen på egenskapen **PendingTasks**. Rapporten hittar den tjänst instans som har problem, men den fångar inte in situationen där instansen är uppifrån. Rapporterna rensas sedan. Den kan rapportera om den sekundära tjänsten. Om den sekundära aktiviteten slutförs rensar den sekundära instansen rapporten från butiken. Rapporten fångar inte in situationen där bekräftelse meddelandet går förlorat och aktiviteten är inte avslutad från huvud punkten i vyn.

Rapporteringen görs i de fall som beskrivs ovan, rapporterna samlas in i program hälsan när hälso tillståndet utvärderas.

## <a name="report-periodically-vs-on-transition"></a>Rapportera regelbundet kontra vid över gång
Med hjälp av hälso rapport modellen kan övervaknings enheten skicka rapporter regelbundet eller via över gångar. Det rekommenderade sättet för rapportering av övervaknings enheten sker regelbundet, eftersom koden är mycket enklare och mindre känslig för fel. Övervaknings gruppen måste sträva efter att vara så enkla som möjligt för att undvika buggar som utlöser fel rapporter. Felaktiga *rapporter påverkar hälso utvärderingen* och scenarier baserat på hälso tillstånd, inklusive uppgraderingar. Fel *felfria* rapporter döljer problem i klustret, vilket inte önskas.

För periodisk rapportering kan övervaknings enheten implementeras med en timer. Vid ett timer-motanrop kan övervaknings enheten kontrol lera statusen och skicka en rapport som baseras på det aktuella läget. Du behöver inte se vilken rapport som tidigare har skickats eller göra eventuella optimeringar vad gäller meddelande hantering. Hälso klienten har en kommando insamlings logik som hjälper dig med prestanda. Medan hälso klienten hålls igång, görs ett nytt försök internt tills rapporten bekräftas av hälso lagret eller tills en ny rapport med samma entitet, egenskap och källa genereras.

Rapportering av över gångar kräver noggrann hantering av tillstånd. Övervaknings enheten övervakar vissa villkor och rapporter endast när villkoren ändras. Den andra metoden är att färre rapporter behövs. Nack delen är att logiken i övervaknings enheten är komplex. Övervaknings enheten måste underhålla villkoren eller rapporterna, så att de kan kontrol leras för att fastställa tillstånds ändringar. Vid redundans måste det vara viktigt med rapporter som lagts till, men som ännu inte skickats till hälso lagret. Sekvensnummer måste vara allt fler. Annars avvisas rapporterna som inaktuella. I sällsynta fall där data går förlorade kan synkroniseringen krävas mellan tillståndet hos rapportören och hälso lagrets tillstånd.

Rapportering av över gångar är begripligt för rapportering i tjänster, via `Partition` eller `CodePackageActivationContext` . När det lokala objektet (replik eller distribuerat tjänst paket/distribuerat program) tas bort, tas även alla rapporter bort. Den här automatiska rensningen sänker behovet av synkronisering mellan rapporterings-och hälso lager. Om rapporten är för överordnad partition eller överordnat program, måste du vara försiktig med redundans för att undvika inaktuella rapporter i hälso lagret. Logik måste läggas till för att behålla rätt tillstånd och rensa rapporten från Store när de inte behövs längre.

## <a name="implement-health-reporting"></a>Implementera hälso rapportering
När entiteten och rapport informationen är tydliga kan sändning av hälso rapporter göras via API, PowerShell eller REST.

### <a name="api"></a>API
Om du vill rapportera genom API: et måste du skapa en hälso rapport som är speciell för den entitetstyp som de vill rapportera om. Ge rapporten till en hälso klient. Du kan också skapa en hälso information och skicka den till rätt rapporterings metoder på `Partition` eller `CodePackageActivationContext` rapportera om aktuella entiteter.

I följande exempel visas periodiska rapporter från en övervaknings enhet i klustret. Övervaknings enheten kontrollerar om en extern resurs kan nås från en nod. Resursen krävs av ett tjänst manifest i programmet. Om resursen inte är tillgänglig kan de andra tjänsterna i programmet fortfarande fungera som de ska. Rapporten skickas därför till entiteten distribuerade tjänst paket var 30: e sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Skicka hälso rapporter med **send-ServiceFabric *EntityType* HealthReport**.

I följande exempel visas periodiska rapporter om processor värden på en nod. Rapporterna ska skickas var 30: e sekund och de har tid att leva på två minuter. Om de går ut har rapportören problem, så noden utvärderas vid fel. När processorn är över ett tröskelvärde har rapporten ett hälso tillstånd för varningen. När CPU: n är över ett tröskelvärde på mer än den konfigurerade tiden rapporteras den som ett fel. Annars skickar rapportören ett hälso tillstånd för OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

I följande exempel rapporteras en tillfällig varning på en replik. Först får du partitions-ID: t och sedan replik-ID: t för tjänsten som det är intresse rad av. Den skickar sedan en rapport från **PowershellWatcher** på egenskapen **ResourceDependency**. Rapporten är av intresse för bara två minuter och tas bort automatiskt från Store.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Skicka hälso rapporter med REST med POST-begäranden som går till önskad entitet och har i texten hälso rapport beskrivningen. Se till exempel hur du skickar REST-rapporter för [kluster hälsa](/rest/api/servicefabric/report-the-health-of-a-cluster) eller [tjänste hälso rapporter](/rest/api/servicefabric/report-the-health-of-a-service). Alla entiteter stöds.

## <a name="next-steps"></a>Nästa steg
Beroende på hälso data kan tjänst skribenter och kluster/program administratörer tänka på hur du använder informationen. De kan till exempel ställa in aviseringar baserat på hälso status för att fånga allvarliga problem innan de provoke avbrott. Administratörer kan också konfigurera reparations system för att åtgärda problem automatiskt.

[Introduktion till Service Fabric Health Monitoring](service-fabric-health-introduction.md)

[Visa Service Fabric hälso rapporter](service-fabric-view-entities-aggregated-health.md)

[Rapportera och kontrol lera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Använda system hälso rapporter för fel sökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric program uppgradering](service-fabric-application-upgrade.md)
