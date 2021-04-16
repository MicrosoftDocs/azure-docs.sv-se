---
title: Azure Service Fabric-versioner
description: Information om Azure-Service Fabric. Innehåller information om de senaste funktionerna och förbättringarna i Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 2a035f531e03dc42e8be4f3dab403406eb7c8f14
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518634"
---
# <a name="service-fabric-releases"></a>Service Fabric versioner

Den här artikeln innehåller mer information om de senaste versionerna och uppdateringarna av Service Fabric runtime och SDK:er.

Följande resurser är också tillgängliga:
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Felsökningsguider</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Ärendespårning</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Supportalternativ</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versioner som stöds</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kodexempel</a>


## <a name="service-fabric-80"></a>Service Fabric 8.0

Vi är glada över att kunna meddela att 8.0-versionen av Service Fabric runtime har börjat distribueras till de olika Azure-regionerna tillsammans med verktygs- och SDK-uppdateringar. Uppdateringarna för .NET SDK, Java SDK och Service Fabric runtime är tillgängliga via installationsprogrammet för webbplattformen, NuGet-paket och Maven-lagringsplatsen.

### <a name="key-announcements"></a>Viktiga meddelanden

- **Allmän tillgänglighet** för stöd för .NET 5 för Windows
- **Allmän tillgänglighet** för [tillståndslösa NodeTypes](https://docs.microsoft.com/azure/service-fabric/service-fabric-stateless-node-types)
- Möjlighet att flytta tillståndslösa tjänstinstanser
- Möjlighet att lägga till parametriserad DefaultLoad i programmanifestet
- För uppgraderingar av singleton-repliker – möjlighet att ha några av inställningarna på klusternivå som ska definieras på programnivå
- Möjlighet till smart placering baserat på nodtaggar
- Möjlighet att definiera tröskelvärde för procentandel för noder med feltillstånd som påverkar klustrets hälsa
- Möjlighet att köra frågor mot de mest inlästa tjänsterna
- Möjlighet att lägga till ett nytt intervall för nya felkoder
- Möjlighet att markera tjänstinstansen som slutförd
- Stöd för vågbaserad distributionsmodell för automatiska uppgraderingar
- Beredskapsavsökning har lagts till för program i containrar
- Aktivera UseSeparateSecondaryMoveCost till true som standard
- StateManager har åtgärdats för att släppa referensen så snart som säker att släppas
- Blockera borttagning av Central Secret Service vid lagring av användarhemligheter


### <a name="service-fabric-80-releases"></a>Service Fabric 8.0-versioner
| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 8 april 2021 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|


## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Viktiga meddelanden

- **Förhandsversion:** [**Service Fabric hanterade kluster**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) är nu i offentlig förhandsversion. Service Fabric hanterade kluster syftar till att förenkla klusterdistribution och -hantering genom att kapsla in de underliggande resurser som utgör ett Service Fabric-kluster till en enda ARM-resurs. Mer information finns i Service Fabric [översikt över hanterade kluster.](./overview-managed-cluster.md)
- **Förhandsversion:** [**Stöd för tillståndslösa tjänster med ett antal instanser större än antalet noder är**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) nu i offentlig förhandsversion. En placeringsprincip gör det möjligt att skapa flera tillståndslösa instanser av en partition på en nod.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) är nu tillgängligt.
    - Nu kan du köra FabricObserver i Linux- och Windows-kluster.
    - Nu kan du skapa anpassade plugin-program för observerare. Mer information [och kod finns i readme](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) för [plugin-program](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) och exempelprojektet.
    - Nu kan du ändra valfri övervakningsinställning via uppgradering av programparametrar. Det innebär att du inte längre behöver distribuera om FO för att ändra specifika övervakningsinställningar. Se [exemplet](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Stöd för Ubuntu 18.04 OneBox-containeravbildningar**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Förhandsversion:** [ **KeyVault-referens för Service Fabric-program stöder **ENDAST versionshemligheter.** Hemligheter utan versioner stöds inte.**](./service-fabric-keyvault-references.md)
- SF SDK kräver den senaste VS 2019-uppdateringen 16.7.6 eller 16.8 förhandsversion 4 för att kunna skapa nya .NET Framework-projekt med tillståndslösa/tillståndslösa/aktörs-projekt. Om du inte har den senaste VS-uppdateringen kan du när du har skapat tjänstprojektet använda pakethanteraren för att installera Microsoft.ServiceFabric.Services (version 4.2.x) för tillståndslösa/tillståndslösa projekt och Microsoft.ServiceFabric.Actors (version 4.2.x) för aktörsprojekt från nuget.org.
- **RunToCompletion:** Service Fabric har stöd för körning till slutförande för körbara gästdatorer. Med den här uppdateringen när repliken har slutförts släpps klusterresurserna som allokerats till den här repliken.
- [**Stöd för resursstyrning har förbättrats:**](./service-fabric-resource-governance.md)tillåter begäranden och gränser för cpu- och minnesresurser.

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2-versioner
| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 21 oktober 2020 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| Den 9 november 2020 | [Uppdateringsutgå Service Fabric version 7.2 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| Den 10 november 2020  | Tredje uppdateringsutgågåren för Azure Service Fabric 7.2 | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| Den 2 december 2020 | [Azure Service Fabric version 7.2 fjärde uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| Den 25 januari 2021 | [Femte uppdateringsutgågåren för Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| Den 17 februari 2021 | [Azure Service Fabric 7.2 sjätte uppdateringsutgåfinan](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| Den 10 mars 2021 | [Azure Service Fabric 7.2, sjudliga uppdateringsutgågår](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

På grund av den nuvarande COVID-19-veckan, och med hänsyn till de utmaningar som våra kunder står inför, gör vi 7.1 tillgängligt, men kommer inte automatiskt att uppgradera kluster som är inställda på att ta emot automatiska uppgraderingar. Vi pausar automatiska uppgraderingar tills vidare för att säkerställa att kunderna kan tillämpa uppgraderingar när det är lämpligast för dem, för att undvika oväntade avbrott.

Du kommer att kunna uppdatera till 7.1 via [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal) eller via [en Azure Resource Manager distribution](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Service Fabric kluster med automatiska uppgraderingar aktiverade börjar ta emot 7.1-uppdateringen automatiskt när vi återupptar standardproceduren för att distribuera. Vi kommer att ge ett nytt meddelande innan standard-distribuera börjar på [Service Fabric Tech Community Site](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Vi har också publicerat uppdateringar för att avsluta supportdatumet för större versioner från 6.5 till och med 7.1 [här.](./service-fabric-versions.md) 

#### <a name="key-announcements"></a>Viktiga meddelanden

- **Allmän tillgänglighet** för [ **Service Fabric hanterade identiteter för Service Fabric program**](./concepts-managed-identity.md)
- [**Stöd för Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - Förhandsversion: Stöd för tillfälliga [**OS-diskar med**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)VM-skalningsuppsättning **: Tillfälliga OS-diskar är lagringsutrymme som skapas på den lokala virtuella datorn och sparas inte till fjärranslutna Azure Storage. De rekommenderas för alla Service Fabric nodtyper (primär och sekundär), eftersom de är tillfälliga OS-diskar jämfört med traditionella beständiga OS-diskar:
      -  Minska svarstiden för läsning/skrivning till OS-disk
      -  Snabbare återställning/avbildning av nodhanteringsåtgärder
      -  Minska de totala kostnaderna (diskarna är kostnadsfria och medför inga ytterligare lagringskostnader)
- Stöd för deklaration av [**tjänstslutpunktscertifikat för Service Fabric program efter ämnesnamnet**](./service-fabric-service-manifest-resources.md).
- [**Stöd för hälsoavsökningar för containertjänster:**](./probes-codepackage.md)Stöd för Liveness Probe-mekanism för containeriserade program. Livenessavsökning hjälper till att meddela det containeriserade programmets liveness och när de inte svarar inom rimlig tid resulterar det i en omstart. 
- [**Stöd för initiering av kodpaket**](./initializer-codepackages.md) för [containrar](./service-fabric-containers-overview.md) och [körbara gästprogram.](./service-fabric-guest-executables-introduction.md) På så sätt kan kodpaket (t.ex. containrar) köras i en angiven ordning för att utföra initiering av tjänstpaketet.
- **FabricObserver och ClusterObserver är** tillståndslösa program som samlar Service Fabric telemetri som är relaterade till olika aspekter av ett SF-kluster. Båda dessa program är redo för distribution till Windows-produktionskluster för att samla in omfattande telemetri med implementerat stöd för ApplicationInsights, EventSource och LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)– körs på alla noder, genererar hälsohändelser, genererar telemetri när användarkonfigurerade tröskelvärden för resursanvändning nås. Den här versionen innehåller flera förbättringar för övervakning, datahantering, information om hälsohändelse och strukturerad telemetri.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) – körs på en nod och samlar in hälsotelemetri på klusternivå. I den här versionen övervakar ClusterObserver även nodstatus och skickar telemetri när noden är ur funktion/inaktiverad längre än den användarangivna tidsperioden.

#### <a name="improve-application-life-cycle-experience"></a>Förbättra programmets livscykelupplevelse

- **[Förhandsversion:Tömning av](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** begäran: Under planerat tjänstunderhåll, till exempel tjänstuppgraderingar eller nodinaktivering, vill du tillåta att tjänsterna tömmer anslutningar på ett smidigt sätt. Den här funktionen lägger till en varaktighet för instansens stängningsfördröjning i tjänstkonfigurationen. Under planerade åtgärder tar SF bort tjänstens adress från identifieringen och väntar sedan denna tid innan tjänsten stängs av.
- **[Automatisk identifiering och utjämning av underklusion:](./cluster-resource-manager-subclustering.md)** Underklusion sker när tjänster med olika placeringsbegränsningar har ett gemensamt belastningsmått . [](./service-fabric-cluster-resource-manager-metrics.md) Om belastningen på de olika uppsättningar noderna skiljer sig avsevärt, Service Fabric Klusterresurshanteraren anser att klustret är obalanserat, även om det har bästa möjliga balans på grund av placeringsbegränsningarna. Därför försöker den balansera om klustret, vilket potentiellt orsakar onödiga tjänstförflyttningar (eftersom "obalansen" inte kan förbättras avsevärt). Från och med den här versionen kommer Klusterresurshanteraren nu att försöka identifiera den här typen av konfigurationer automatiskt och förstå när obalansen kan åtgärdas genom förflyttning, och när den i stället bör lämna saker och ting i sig eftersom det inte går att göra några betydande förbättringar.  
- [**Olika flyttkostnader för sekundära**](./service-fabric-cluster-resource-manager-movement-cost.md)repliker: Vi har introducerat det nya flytta-kostnadsvärdet VeryHigh som ger ytterligare flexibilitet i vissa scenarier för att definiera om en separat flyttkostnad ska användas för sekundära repliker.
- Aktiverad [**liveavsökningsmekanism**](./probes-codepackage.md) för containerprogram. Livenessavsökning hjälper till att meddela det containeriserade programmets liveness och när de inte svarar inom rimlig tid resulterar det i en omstart.
- [**Kör till slutförande/en gång för tjänster**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Avbildningsarkiv förbättringar
 - Service Fabric 7.1 använder **anpassad transport för att skydda filöverföring mellan noder som standard.** Beroendet av SMB-filresursen tas bort från version 7.1. De skyddade SMB-filresurser finns fortfarande på noder som innehåller Avbildningsarkiv Service-replik för kundens val att avanmäla sig från standard och för uppgradering och nedgradering till gammal version.
       
 #### <a name="reliable-collections-improvements"></a>Förbättringar av Reliable Collections

- I minnet lagras endast stöd för tillståndsful-tjänster som använder [**Reliable Collections:**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)Volatile Reliable Collections gör att data kan bevaras på disk för hållbarhet mot storskaliga avbrott, kan användas för arbetsbelastningar som replikerad cache, till exempel där tillfällig dataförlust kan tolereras. Baserat på begränsningarna och begränsningarna i [Volatile Reliable Collections](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)rekommenderar vi detta för arbetsbelastningar som inte behöver beständighet för tjänster som hanterar de sällsynta tillfällena av kvorumförlust.
- [**Förhandsversion: Service Fabric Backup-utforskaren:**](https://github.com/microsoft/service-fabric-backup-explorer)För att underlätta hanteringen av Reliable Collections-säkerhetskopior för Service Fabric tillståndsful-program kan Service Fabric Backup-utforskaren användare att
    - Granska och granska innehållet i Reliable Collections,
    - Uppdatera aktuellt tillstånd till en konsekvent vy
    - Skapa en säkerhetskopia av den aktuella ögonblicksbilden av Reliable Collections
    - Åtgärda skadade data
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1-versioner
| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 20 april 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Viktig information](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| Den 16 juni 2020 | [Microsoft Azure Service Fabric 7.1 Första uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| Den 20 juli 2020 | [Microsoft Azure Service Fabric uppdatering 7,1 sekund](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| Den 12 augusti 2020 | [Microsoft Azure Service Fabric 7.1 tredje uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| Den 10 september 2020 | [Microsoft Azure Service Fabric 7.1 fjärde uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| Den 7 oktober 2020 | Microsoft Azure Service Fabric 7.1 sjätte uppdatering | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| Den 23 november 2020 | Microsoft Azure Service Fabric 7.1 1 uppdatering | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 är nu tillgängligt! Du kommer att kunna uppdatera till 7.0 via Azure Portal eller via en Azure Resource Manager distribution. På grund av kundfeedback om versioner kring helgdagen börjar vi inte automatiskt uppdatera kluster som är inställda på att få automatiska uppgraderingar förrän i januari.
I januari återupptar vi standardproceduren för utrullning och kluster med automatiska uppgraderingar aktiverade börjar ta emot 7.0-uppdateringen automatiskt. Vi kommer att ge ett nytt meddelande innan utrullningen påbörjas.
Vi kommer också att uppdatera våra planerade lanseringsdatum för att indikera att vi tar hänsyn till den här principen. Leta här efter uppdateringar om våra framtida [lanseringsscheman.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Viktiga meddelanden
 - [**KeyVaultReference-stöd**](./service-fabric-keyvault-references.md)för programhemligheter (förhandsversion): Service Fabric-program som har aktiverat hanterade identiteter kan nu direkt referera till en Key Vault-hemlig URL som en miljövariabel, programparameter eller [autentiseringsuppgifter](./concepts-managed-identity.md) för containerdatabasen. Service Fabric automatiskt hemligheten med hjälp av programmets hanterade identitet. 
     
- **Förbättrad uppgraderingssäkerhet för** tillståndslösa tjänster: För att garantera tillgänglighet under en programuppgradering har vi infört nya konfigurationer för att definiera det minsta antalet instanser för [tillståndslösa](/dotnet/api/system.fabric.description.statelessservicedescription) tjänster som ska anses vara tillgängliga. Tidigare var det här värdet 1 för alla tjänster och kunde inte ändras. Med den här nya säkerhetskontrollen per tjänst kan du se till att dina tjänster behåller ett minsta antal instanser under programuppgraderingar, klusteruppgraderingar och annat underhåll som förlitar sig på Service Fabric:s hälso- och säkerhetskontroller.
  
- [**Resursbegränsningar för användartjänster:**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)Användare kan konfigurera resursgränser för användartjänsterna på en nod för att förhindra scenarier som resursutmattning av Service Fabric-systemtjänsterna. 
  
- [**Mycket hög tjänstflyttningskostnad**](./service-fabric-cluster-resource-manager-movement-cost.md) för en repliktyp. Repliker med mycket hög flyttkostnad flyttas bara om det finns en begränsningsöverträdelse i klustret som inte kan åtgärdas på något annat sätt. Se det länkade dokumentet för ytterligare information om när användningen av en "mycket hög" flyttkostnad är rimlig och för ytterligare överväganden.
  
-  **Ytterligare klustersäkerhetskontroller:** I den här versionen introducerade vi en konfigurerbar säkerhetskontroll för seed node-kvorum. På så sätt kan du anpassa hur många starttidsnoder som måste vara tillgängliga under klustrets livscykel och hanteringsscenarier. Åtgärder som skulle ta klustret under det konfigurerade värdet blockeras. I dag är standardvärdet alltid ett kvorum för startvärdesnoderna. Om du till exempel har 7 startvärdesnoder blockeras en åtgärd som tar dig under 5 startvärdesnoder som standard. Med den här ändringen kan du göra det lägsta säkra värdet 6, vilket endast skulle tillåta att en startvärdesnod ligger nere i taget.
   
- Stöd har lagts till [**för att hantera säkerhetskopierings- och återställningstjänsten i Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Detta gör följande aktiviteter möjliga direkt från SFX: identifiering av säkerhetskopierings- och återställningstjänsten, skapande av säkerhetskopieringspolicy, aktivering av automatiska säkerhetskopieringar, tagande av adhoc-säkerhetskopieringar, utlösande återställningsåtgärder och bläddring av befintliga säkerhetskopior.

- Meddelande om tillgänglighet för [**ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)Det här verktyget hjälper dig att verifiera att typer som används i tillförlitliga samlingar är framåt- och bakåtkompatibla under en löpande programuppgradering. Detta förhindrar uppgraderingsfel eller dataförlust och skadade data på grund av saknade eller inkompatibla typer.

- [**Aktivera stabila läsningar på sekundära repliker:**](./service-fabric-reliable-services-configuration.md#configuration-names-1)Stabila läsningar begränsar sekundära repliker till att returnera värden som har kvorum-acked.

Dessutom innehåller den här versionen andra nya funktioner, felkorrigeringar och support, tillförlitlighet och prestandaförbättringar. En fullständig lista över ändringar finns i den här [versionen.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 18 november 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| Den 30 januari 2020 | [Uppdateringsutgå Service Fabric Version av Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| Den 6 februari 2020 | [Uppdateringsutgå Service Fabric Version av Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| Den 2 mars 2020 | [Uppdateringsutgå Service Fabric Version av Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| Den 6 maj 2020 | [Uppdateringsutgå Service Fabric version 7.0 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| Den 9 oktober 2020 | Azure Service Fabric 7.0,0, nionde uppdateringsutgås | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Den här versionen innehåller support, tillförlitlighet och prestandaförbättringar, nya funktioner, felkorrigeringar och förbättringar för att underlätta livscykelhanteringen för kluster och program.

> [!IMPORTANT]
> Service Fabric 6.5 är den slutliga versionen med stöd Service Fabric verktyg i Visual Studio 2015. Kunder rekommenderas att gå över till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) framöver.

Vad är nytt i Service Fabric 6.5:

- Service Fabric Explorer innehåller en [Avbildningsarkiv Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) för att granska program som du har laddat upp till bildarkivet.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) innehåller många förbättringar av självdiagnostik. Kunder med POA rekommenderas att flytta till den här versionen.

- [EventStore-tjänsten är aktiverad som standard](service-fabric-visualizing-your-cluster.md#event-store) för Service Fabric 6.5-kluster om du inte har valt bort det.

- Livscykelhändelser [för repliker](service-fabric-diagnostics-event-generation-operational.md#replica-events) har lagts till för tillståndsful-tjänster.

- [Bättre synlighet för startvärdets nodstatus,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)inklusive varningar på klusternivå om en startvärdetod är skadad (*Ned,* *Borttagen* eller *Okänd).*

- [Service Fabric programåterställningsverktyget gör](https://github.com/Microsoft/Service-Fabric-AppDRTool) att Service Fabric tillståndsfulla tjänster kan återställas snabbt när det primära klustret påträffar en katastrof. Data från det primära klustret synkroniseras kontinuerligt i det sekundära väntelägesprogrammet med hjälp av regelbunden säkerhetskopiering och återställning.

- Visual Studio för publicering [av .NET Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) installeras automatiskt för Service Fabric 6.5 (och senare versioner) när du uppgraderar eller skapar ett nytt Linux-kluster i Azure.

- [SFCTL](./service-fabric-cli.md) installeras som standard i MacOS/Linux OneBox-kluster.

Mer information finns i [versionsinformation Service Fabric 6.5.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 11 juni 2019 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| Den 2 juli 2019 | [Uppdateringsutgå Service Fabric 6.5 för Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Uppdateringsutgå Service Fabric 6.5 för Azure Service Fabric](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 augusti 2019 | [Uppdateringsutgå Service Fabric 6.5 för Azure Service Fabric](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| Den 14 oktober 2019 | [Uppdateringsutgå Service Fabric 6.5 för Azure Service Fabric](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Information] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 30 november 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| Den 12 december 2018 | [Azure Service Fabric 6.4 Refresh Release for Windows clusters (Uppdatera version 6.4 för Windows-kluster)](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| Den 4 februari 2019 | [Uppdateringsutgå Service Fabric version 6.4 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| Den 4 mars 2019 | [Uppdateringsutgå Service Fabric version 6.4 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| Den 8 april 2019 | [Uppdateringsutgå Service Fabric version 6.4 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| Den 2 maj 2019 | [Uppdateringsutgå Service Fabric version 6.4 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| Den 28 maj 2019 | [Uppdateringsutgå Service Fabric version 6.4 av Azure Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
