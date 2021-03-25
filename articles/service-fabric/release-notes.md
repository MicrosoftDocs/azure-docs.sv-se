---
title: Azure Service Fabric-versioner
description: Viktig information för Azure Service Fabric. Innehåller information om de senaste funktionerna och förbättringarna i Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 7f99c37cd9f029c7478d4102bd17c952ffd52f8c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044285"
---
# <a name="service-fabric-releases"></a>Service Fabric versioner

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Fel söknings guider</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Ärende spårning</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Supportalternativ</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versioner som stöds</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kodexempel</a>

Den här artikeln innehåller mer information om de senaste versionerna och uppdateringarna av Service Fabric Runtime och SDK: er.

## <a name="service-fabric-72"></a>Service Fabric 7,2

Vi är glada över att kunna meddela att 7,2-versionen av Service Fabric Runtime har börjat distribueras till de olika Azure-regionerna tillsammans med verktyg och SDK-uppdateringar. Uppdateringarna för .NET SDK, Java SDK och Service Fabric runtime är tillgängliga via installations programmet för webb plattform, NuGet-paket och maven-databaser.

### <a name="key-announcements"></a>Viktiga meddelanden

- För **hands version**: [**Service Fabric hanterade kluster**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) finns nu i offentlig för hands version. Service Fabric hanterade kluster syftar till att förenkla kluster distribution och hantering genom att kapsla in de underliggande resurserna som utgör ett Service Fabric kluster i en enda ARM-resurs. Mer information finns i [Översikt över Service Fabric hanterat kluster](./overview-managed-cluster.md).
- För **hands version**: [**stöd för tillstånds lösa tjänster med ett antal instanser som är större än antalet noder**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) finns nu i offentlig för hands version. En placerings princip gör det möjligt att skapa flera tillstånds lösa instanser av en partition på en nod.
- [**FabricObserver (fo) 3,0**](https://aka.ms/sf/fabricobserver) är nu tillgängligt.
    - Nu kan du köra FabricObserver i Linux-och Windows-kluster.
    - Nu kan du bygga anpassade underlägg-plugin-program. Se plugin-programmet [README](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) och [exempel-plugin-projektet](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) för information och kod.
    - Du kan nu ändra alla observatörs inställningar via uppgradering av program parametrar. Det innebär att du inte längre behöver distribuera om för att ändra de angivna inställningarna för övervakare. Se [exemplet](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Stöd för Ubuntu 18,04 Onebox behållaravbildningen-behållar avbildningar**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- För **hands** version: nyckel [ **valvs referens för Service Fabric program har endast stöd för **versions hemligheter**. Hemligheter utan versioner stöds inte.**](./service-fabric-keyvault-references.md)
- SF SDK kräver den senaste VS 2019-uppdateringen 16.7.6 eller 16,8 Preview 4 för att kunna skapa nya .NET Framework tillstånds lösa/tillstånds känsliga/skådespelare projekt. Om du inte har den senaste VS-uppdateringen när du har skapat tjänst projektet använder du Package Manager för att installera Microsoft. ServiceFabric. Services (version 4.2. x) för tillstånds känsliga/tillstånds lösa projekt och Microsoft. ServiceFabric. skådespelare (version 4.2. x) för skådespelare-projekt från nuget.org.
- **RunToCompletion**: Service Fabric stöder koncept körning till slut för ande av gäst-körbara filer. Med den här uppdateringen kommer de kluster resurser som allokeras till den här repliken att frisläppas när repliken har körts.
- [**Resurs styrnings stödet har förbättrats**](./service-fabric-resource-governance.md): tillåter förfrågningar och begränsar specifikationer för processor-och minnes resurser.

### <a name="service-fabric-72-releases"></a>Service Fabric 7,2-versioner
| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 21 oktober 2020 | [Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 november 2020 | [Azure Service Fabric 7,2 andra uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 november 2020  | Azure Service Fabric 7,2 tredje uppdaterings version | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 december 2020 | [Azure Service Fabric 7,2 fjärde uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 januari 2021 | [Azure Service Fabric 7,2 femte uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 februari 2021 | [Azure Service Fabric 7,2 sjätte uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 mars 2021 | [Azure Service Fabric 7,2 sjunde uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)

## <a name="previous-versions"></a>Tidigare versioner

### <a name="service-fabric-71"></a>Service Fabric 7,1

På grund av den aktuella COVID-tjänstekrisen och med tanke på de utmaningar som våra kunder möter, gör vi 7,1 tillgängliga, men uppgraderar inte automatiskt kluster som är inställda på att ta emot automatiska uppgraderingar. Vi pausar automatiska uppgraderingar tills vidare se till att kunderna kan installera uppgraderingar när de är mest lämpliga för dem, för att undvika oväntade avbrott.

Du kommer att kunna uppdatera till 7,1 via [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) eller via en [Azure Resource Manager distribution](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Service Fabric kluster med aktiverade automatiska uppgraderingar börjar ta emot 7,1-uppdateringen automatiskt när vi fortsätter med standard distributions proceduren. Vi kommer att tillhandahålla ett annat meddelande innan standard distributionen börjar på den [Service Fabric tekniska Community-webbplatsen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Vi har också publicerat uppdateringar till slutet av support datumet för större versioner från 6,5 till 7,1 [här](./service-fabric-versions.md#supported-versions). 

#### <a name="key-announcements"></a>Viktiga meddelanden

- **Allmän tillgänglighet** för [ **Service Fabric hanterade identiteter för Service Fabric program**](./concepts-managed-identity.md)
- [**Stöd för Ubuntu 18,04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - För [**hands version: stöd för virtuell dator med den virtuella datorns skalnings uppsättning**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: de tillfälliga OS-diskarna skapas på den lokala virtuella datorn och sparas inte på fjärrAzure Storage. De rekommenderas för alla Service Fabric Node-typer (primär och sekundär), på grund av traditionella beständiga OS-diskar, tillfälliga OS-diskar:
      -  Minska svars tiden för Läs/skriv till OS-disk
      -  Aktivera snabbare återställnings-och avbildnings hanterings åtgärder
      -  Minska totalkostnaden (diskarna är kostnads fria och debiteras inga ytterligare lagrings kostnader)
- Stöd för deklaration av [**tjänst slut punkts certifikat för Service Fabric program efter eget ämnes namn**](./service-fabric-service-manifest-resources.md).
- [**Stöd för hälso avsökningar för containerbaserade tjänster**](./probes-codepackage.md): stöd för direktmigreringens avsöknings funktion för program i behållare. Med hjälp av direktmigreringens avsökning kan du meddela om liveheten för det behållar programmet och när de inte svarar inom rimlig tid, vilket leder till en omstart. 
- [**Stöd för initierare kod paket**](./initializer-codepackages.md) för [behållare](./service-fabric-containers-overview.md) och [körbara gäst](./service-fabric-guest-executables-introduction.md) program. Detta gör det möjligt att köra kod paket (t. ex. behållare) i en angiven ordning för att utföra service paket initiering.
- **FabricObserver och ClusterObserver** är tillstånds lösa program som fångar Service Fabric telemetri som är relaterade till olika aspekter av ett SF-kluster. Båda programmen är klara att distribueras till Windows-produktionsanläggningar för att samla in omfattande telemetri med implementerat stöd för ApplicationInsights, EventSource och LogAnalytics.
    - [**FabricObserver (fo) 2,0**](https://github.com/microsoft/service-fabric-observer)-körs på alla noder, genererar hälso händelser och genererar telemetri när de angivna tröskelvärdena för resursanvändning nås. Den här versionen innehåller flera förbättringar i övervakning, data hantering, hälso information, strukturerad telemetri.
     - [**ClusterObserver (co) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -körs på en nod, fångar upp hälso telemetri på kluster nivå. I den här versionen övervakar ClusterObserver också nodens status och genererar telemetri när noden är nere/inaktive rad under längre tid än den användardefinierade tids perioden.

#### <a name="improve-application-life-cycle-experience"></a>Förbättra programmets livs cykel upplevelse

- För **[hands version: begär dränering](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: under planerat underhåll av tjänster, till exempel tjänst uppgraderingar eller Node-inaktive ring, vill du tillåta tjänsterna att tömma anslutningarna på ett smidigt sätt. Den här funktionen lägger till en instans stängnings fördröjning i tjänst konfigurationen. Under de planerade åtgärderna tar SF bort tjänstens adress från identifieringen och väntar sedan den här varaktigheten innan tjänsten stängs av.
- **[Automatisk identifiering och balansering av under kluster](./cluster-resource-manager-subclustering.md)**: under kluster sker när tjänster med olika placerings begränsningar har ett gemensamt [belastnings mått](./service-fabric-cluster-resource-manager-metrics.md). Om belastningen på de olika uppsättningarna med noder skiljer sig avsevärt, anser Service Fabric Cluster Resource Manager att klustret är obalanserat, även om det har det bästa möjliga saldot på grund av placerings begränsningarna. Därför försöker det att balansera om klustret, vilket kan orsaka onödiga tjänst transporter (eftersom "obalans" inte kan förbättras avsevärt). Från och med den här versionen försöker kluster resurs hanteraren nu att automatiskt identifiera de här sorteringen av konfigurationer och förstå när obalansen kan åtgärdas genom flyttningen, och i stället bör den lämna saker som är ensamma eftersom ingen avsevärd förbättring kan göras.  
- [**Olika flytt kostnader för sekundära repliker**](./service-fabric-cluster-resource-manager-movement-cost.md): vi har lanserat ett nytt flytt kostnads värde VeryHigh som ger ytterligare flexibilitet i vissa scenarier för att definiera om en separat flytt kostnad ska användas för sekundära repliker.
- Aktive rad [**direktmigreringens avsöknings**](./probes-codepackage.md) funktion för program i behållare. Med hjälp av direktmigreringens avsökning kan du meddela om liveheten för det behållar programmet och när de inte svarar inom rimlig tid, vilket leder till en omstart.
- [**Kör till slut för ande/en gång för tjänster**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Avbildningsarkiv förbättringar
 - Service Fabric 7,1 använder **anpassad transport för att skydda fil överföring mellan noder som standard**. Beroendet av SMB-filresursen tas bort från version 7,1. De skyddade SMB-filresurserna finns fortfarande på noder som innehåller Avbildningsarkiv tjänst replik för kundens val att välja mellan standard och för uppgradering och degradering till gammal version.
       
 #### <a name="reliable-collections-improvements"></a>Förbättringar av pålitliga samlingar

- [**I minnet lagrar bara stöd för tillstånds känsliga tjänster med Reliable Collections**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): volatile Reliable Collections gör att data kan sparas till disk för hållbarhet mot storskaliga avbrott, kan användas för arbets belastningar som replikerad cache, till exempel där tillfällig data förlust kan tolereras. Baserat på [begränsningar och begränsningar för flyktiga pålitliga samlingar](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)rekommenderar vi detta för arbets belastningar som inte behöver beständighet, för tjänster som hanterar sällsynta tillfällen för att förlora kvorum.
- För [**hands version: Service Fabric backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): för att under lätta hanteringen av pålitliga säkerhets kopieringar för Service Fabric tillstånds känsliga program kan Service Fabric backup Explorer göra det möjligt för användare att
    - Granska och granska innehållet i de pålitliga samlingarna,
    - Uppdatera aktuellt tillstånd till en konsekvent vy
    - Skapa en säkerhets kopia av den aktuella ögonblicks bilden av de pålitliga samlingarna
    - Åtgärda skadade data
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7,1-versioner
| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 20 april 2020 | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Viktig information](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 juni 2020 | [Microsoft Azure Service Fabric 7,1 första uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 juli 2020 | [Microsoft Azure Service Fabric 7,1 andra uppdatering](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 augusti 2020 | [Microsoft Azure Service Fabric 7,1 tredje uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 september 2020 | [Microsoft Azure Service Fabric 7,1 fjärde uppdateringen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 oktober 2020 | Microsoft Azure Service Fabric 7,1 sjätte uppdateringen | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 november 2020 | Microsoft Azure Service Fabric 7,1 åttondels uppdatering | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 är nu tillgängligt! Du kommer att kunna uppdatera till 7,0 via Azure Portal eller via en Azure Resource Manager distribution. På grund av kundernas feedback om versioner under den helgdags perioden börjar vi inte att automatiskt uppdatera kluster som är inställda för att ta emot automatiska uppgraderingar förrän januari.
I januari kommer vi att återuppta standard proceduren för distributionen och kluster med aktiverade automatiska uppgraderingar börjar ta emot 7,0-uppdateringen automatiskt. Vi kommer att tillhandahålla ett annat meddelande innan distributionen påbörjas.
Vi uppdaterar även våra planerade versions datum för att ange att den här principen ska beaktas. Här hittar du uppdateringar för våra framtida [versions scheman](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).

#### <a name="key-announcements"></a>Viktiga meddelanden
 - [**KeyVaultReference-stöd för program hemligheter (för hands version)**](./service-fabric-keyvault-references.md): Service Fabric program som har aktiverat [hanterade identiteter](./concepts-managed-identity.md) kan nu direkt referera till en Key Vault hemlig URL som en miljö variabel, program parameter eller autentiseringsuppgifter för containerns lagrings plats. Service Fabric kommer automatiskt att lösa hemligheten med hjälp av programmets hanterade identitet. 
     
- **Förbättrad uppgraderings säkerhet för tillstånds lösa tjänster**: för att garantera tillgängligheten under en program uppgradering har vi introducerat nya konfigurationer för att definiera det [minsta antalet instanser för tillstånds lösa tjänster](/dotnet/api/system.fabric.description.statelessservicedescription) som ska anses tillgängliga. Tidigare var det här värdet 1 för alla tjänster och kunde inte ändras. Med den nya säkerhets kontrollen per tjänst kan du se till att tjänsterna behåller ett minsta antal instanser under program uppgraderingar, kluster uppgraderingar och annat underhåll som förlitar sig på Service Fabric hälso-och säkerhets kontroller.
  
- [**Resurs gränser för användar tjänster**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): användare kan konfigurera resurs gränser för användar tjänsterna på en nod för att förhindra scenarier som resurs utbelastning av Service Fabric system tjänster. 
  
- [**Mycket hög service flytt kostnad**](./service-fabric-cluster-resource-manager-movement-cost.md) för en replik typ. Repliker med mycket hög flytt kostnad kommer bara att flyttas om det finns en begränsnings överträdelse i klustret som inte kan åtgärdas på något annat sätt. I det länkade dokumentet finns mer information om när användningen av en "mycket hög" flytt kostnad är rimlig och ytterligare överväganden.
  
-  **Ytterligare säkerhets kontroller för klustret**: i den här versionen har vi infört en konfigurerbar säkerhets kontroll för Dirigerings-nods kvorum. På så sätt kan du anpassa hur många startnoder som måste vara tillgängliga under kluster livs cykel-och hanterings scenarier. Åtgärder som tar klustret lägre än det konfigurerade värdet blockeras. I dag är standardvärdet alltid ett kvorum för startnoderna, till exempel om du har sju startnoder, kommer en åtgärd som tar dig under 5 startnoder att blockeras som standard. Med den här ändringen kan du göra det lägsta säkra värdet 6, vilket innebär att endast en Seed-nod kan vara nere i taget.
   
- Stöd har lagts till för [**att hantera säkerhets kopierings-och återställnings tjänsten i Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Detta gör följande aktiviteter möjligt direkt från SFX: identifiera säkerhets kopierings-och återställnings tjänsten, skapa en säkerhets kopierings princip, aktivera automatisk säkerhets kopiering, använda adhoc-säkerhetskopiering, utlösa återställnings åtgärder och söka efter befintliga säkerhets kopior.

- Vi presenterar tillgängligheten för [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): med det här verktyget kan du validera att de typer som används i pålitliga samlingar är framåtriktade och bakåtkompatibla under en rullande program uppgradering. Detta bidrar till att förhindra uppgraderings fel eller data förlust och skadade data på grund av saknade eller inkompatibla typer.

- [**Aktivera stabil läsning på sekundära repliker**](./service-fabric-reliable-services-configuration.md#configuration-names-1): stabila läsningar begränsar sekundära repliker till att returnera värden som har varit bekräftat.

Dessutom innehåller den här versionen andra nya funktioner, fel korrigeringar och förbättringar av support, tillförlitlighet och prestanda. En fullständig lista över ändringar finns i [viktig information](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Service Fabric 7,0-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| Den 18 november 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Uppdaterings version för Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Uppdaterings version för Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 mars 2020 | [Uppdaterings version för Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 maj 2020 | [Azure Service Fabric 7,0 sjätte uppdaterings version](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 oktober 2020 | Azure Service Fabric 7,0 nionde uppdaterings version | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Den här versionen innehåller förbättringar av support, tillförlitlighet och prestanda, nya funktioner, fel korrigeringar och förbättringar för att förenkla hanteringen av kluster och program livs cykeln.

> [!IMPORTANT]
> Service Fabric 6,5 är den slutliga versionen med stöd för Service Fabric-verktyg i Visual Studio 2015. Kunderna uppmanas att gå vidare till [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Vad är nytt i Service Fabric 6,5:

- Service Fabric Explorer innehåller ett [avbildningsarkiv visnings](service-fabric-visualizing-your-cluster.md#image-store-viewer) program för att inspektera program som du har överfört till avbildnings lagret.

- [POA-1.4.0 (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) innehåller många själv diagnostiska förbättringar. [](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) Kunder med POA rekommenderas att flytta till den här versionen.

- [EventStore-tjänsten är aktive rad som standard](service-fabric-visualizing-your-cluster.md#event-store) i Service Fabric 6,5-kluster om du inte har valt ut.

- Har lagt till [replik livs cykel händelser](service-fabric-diagnostics-event-generation-operational.md#replica-events) för tillstånds känsliga tjänster.

- [Bättre insyn i status för Dirigerings nod](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inklusive varningar på kluster nivå om en *Seed-nod är ohälsosam (av*, *borttagen* eller *okänd*).

- Med [Service Fabric-verktyget för haveri beredskap för program](https://github.com/Microsoft/Service-Fabric-AppDRTool) kan Service Fabric tillstånds känsliga tjänster återställas snabbt när det primära klustret påträffar en katastrof. Data från det primära klustret synkroniseras kontinuerligt i det sekundära standby-programmet med hjälp av periodisk säkerhets kopiering och återställning.

- Visual Studio-stöd för [att publicera .net Core-appar till Linux-baserade kluster](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) installeras automatiskt för Service Fabric 6,5 (och senare versioner) när du uppgraderar eller skapar ett nytt Linux-kluster i Azure.

- [SFCTL](./service-fabric-cli.md) installeras som standard på MacOS/Linux Onebox behållaravbildningen-kluster.

Mer information finns i [versions anmärkningar för Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Service Fabric 6,5-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Viktig information](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 oktober 2019 | [Uppdaterings version för Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Viktig information] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6,4-versioner

| Utgivningsdatum | Frisläpp | Mer information |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4 uppdaterings version för Windows-kluster](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Viktig information](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 mars 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maj 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maj, 2019 | [Uppdaterings version för Azure Service Fabric 6,4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Viktig information](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)