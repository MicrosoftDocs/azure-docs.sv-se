---
title: Resursstyrning för container och tjänster
description: Med Azure Service Fabric kan du ange resurs begär Anden och begränsningar för tjänster som körs som processer eller behållare.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: d760766870c8c2be0a2d2384f6d012b75bc92fbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735666"
---
# <a name="resource-governance"></a>Resursstyrning

När du kör flera tjänster på samma nod eller kluster är det möjligt att en tjänst kan förbruka fler resurser, svälter andra tjänster i processen. Det här problemet kallas för problem med "störningarnas granne". Med Azure Service Fabric kan utvecklaren styra det här beteendet genom att ange begär Anden och gränser per tjänst för att begränsa resursanvändningen.

> Innan du fortsätter med den här artikeln rekommenderar vi att du bekanta dig med [Service Fabric program modellen][application-model-link] och [Service Fabric värd modell][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Resurs styrnings mått

Resurs styrning stöds i Service Fabric i enlighet med [tjänst paketet][application-model-link]. De resurser som är tilldelade till tjänst paketet kan delas ytterligare mellan kod paket. Service Fabric stöder processor-och minnes styrning per tjänst paket, med två inbyggda [mått](service-fabric-cluster-resource-manager-metrics.md):

* *Processor* (Metric name `servicefabric:/_CpuCores` ): en logisk kärna som är tillgänglig på värddatorn. Alla kärnor på alla noder viktas likadant.

* *Minne* (Metric name `servicefabric:/_MemoryInMB` ): minnet uttrycks i megabyte och det mappas till det fysiska minne som är tillgängligt på datorn.

För dessa två mått spårar [kluster resurs hanteraren (CRM)][cluster-resource-manager-description-link] den totala kluster kapaciteten, belastningen på varje nod i klustret och återstående resurser i klustret. Dessa två mått motsvarar andra användare eller anpassade mått. Alla befintliga funktioner kan användas med dem:

* Klustret kan [bal anse](service-fabric-cluster-resource-manager-balancing.md) ras enligt dessa två mått (standard beteende).
* Klustret kan [defragmentas](service-fabric-cluster-resource-manager-defragmentation-metrics.md) enligt dessa två mått.
* När du [beskriver ett kluster][cluster-resource-manager-description-link]kan du ange en buffrad kapacitet för dessa två mått.

> [!NOTE]
> [Rapporter för dynamisk inläsning](service-fabric-cluster-resource-manager-metrics.md) stöds inte för dessa mått. inläsningar för dessa mått definieras vid skapande tillfället.

## <a name="resource-governance-mechanism"></a>Resurs styrnings funktion

Från och med version 7,2 stöder Service Fabric runtime specificering av begär Anden och gränser för processor-och minnes resurser.

> [!NOTE]
> Service Fabric runtime-versioner som är äldre än 7,2 stöder bara en modell där ett enda värde både är **begäran** och **gränsen** för en viss resurs (CPU eller minne). Detta beskrivs som **RequestsOnly** -specifikationen i det här dokumentet.

* *Begär Anden:* Värdena för processor-och minnes förfrågningar representerar de belastningar som används av [kluster resurs hanteraren (CRM)][cluster-resource-manager-description-link] för `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` måtten och. Med andra ord anser CRM resurs förbrukningen för en tjänst som motsvarar dess begär ande värden och använder dessa värden när beslut fattas.

* *Gränser:* Värdena för processor-och minnes gränser representerar de faktiska resurs gränserna som tillämpas när en process eller behållare aktive ras på en nod.

Service Fabric tillåter **RequestsOnly, LimitsOnly** och båda **RequestsAndLimits** -specifikationerna för processor och minne.
* När RequestsOnly-specifikation används använder Service Fabric även värdena för begäran som gränser.
* När LimitsOnly-specifikation används, anser Service Fabric att värdena för begäran är 0.
* När RequestsAndLimits-specifikation används måste gräns värdena vara större än eller lika med värdena för begäran.

För att bättre förstå resurs styrnings mekanismen ska vi titta på ett exempel på placerings scenario med en **RequestsOnly** -specifikation för CPU-resursen (mekanismen för minnes styrning är likvärdig). Överväg en nod med två processor kärnor och två tjänst paket som ska placeras på den. Det första tjänst paketet som ska monteras består av ett enda container kods paket och anger bara en begäran om en processor kärna. Det andra tjänst paketet som ska placeras består av bara ett processbaserade kod paket och anger även en begäran om en processor kärna. Eftersom båda tjänst paketen har en RequestsOnly-specifikation, ställs deras gräns värden in på deras värden för begäran.

1. Först det behållar tjänst paket som begär en processor kärna placeras på noden. Körningen aktiverar behållaren och anger processor gränsen till en kärna. Behållaren kan inte använda mer än en kärna.

2. Därefter placeras det processbaserade tjänst paketet som begär en processor kärna på noden. Körningen aktiverar tjänst processen och anger processor gränsen till en kärna.

I det här läget är summan av förfrågningar lika med nodens kapacitet. CRM kommer inte att placera fler behållare eller tjänst processer med CPU-begäranden på den här noden. På noden körs en process och en behållare med en kärna varje och kommer inte att tävla med varandra för CPU.

Nu ska vi gå tillbaka till vårt exempel med en **RequestsAndLimits** -specifikation. Den här gången anger det behållar tjänst paketet en begäran om en processor kärna och en gräns på två processor kärnor. Det processbaserade tjänst paketet anger både en begäran och en gräns för en processor kärna.
  1. Först det behållar tjänst paketet placeras på noden. Körningen aktiverar behållaren och anger processor gränsen till två kärnor. Behållaren kan inte använda fler än två kärnor.
  2. Därefter placeras det processbaserade tjänst paketet på noden. Körningen aktiverar tjänst processen och anger processor gränsen till en kärna.

  I det här läget är summan av CPU-begäranden för tjänst paket som placeras på noden lika med nodens CPU-kapacitet. CRM kommer inte att placera fler behållare eller tjänst processer med CPU-begäranden på den här noden. Men på noden överskrider summan av gränser (två kärnor för containern + en kärna för processen) kapaciteten hos två kärnor. Om behållaren och process burst samtidigt, finns det risk för processor resursens konkurrens. Sådan konkurrens kommer att hanterade av det underliggande operativ systemet för plattformen. I det här exemplet kan behållaren överföra upp till två processor kärnor, vilket resulterade i att processens begäran om en processor kärna inte garanteras.

> [!NOTE]
> Som vi illustrerade i föregående exempel leder inte värdena för processor och minne **till reservation av resurser på en nod**. De här värdena representerar resursanvändningen som kluster resurs hanteraren anser när du fattar beslut om placering. Limit-värden representerar faktiska resurs gränser som tillämpas när en process eller behållare aktive ras på en nod.


Det finns några situationer där det kan finnas konkurrens för CPU. I dessa fall kan processen och behållaren från vårt exempel uppleva problemet med problem med störningar:

* *Blanda reglerade och icke-styrda tjänster och behållare*: om en användare skapar en tjänst utan någon resurs styrning som har angetts, ser körnings miljön den som förbrukar inga resurser och kan placera den på noden i vårt exempel. I det här fallet förbrukar sig den här nya processen mycket processor vid kostnaden för de tjänster som redan körs på noden. Det finns två lösningar på det här problemet. Du kan antingen inte blanda reglerade och icke-styrda tjänster i samma kluster, eller använda [placerings begränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) så att dessa två typer av tjänster inte slutar på samma uppsättning noder.

* *När en annan process startas på noden, utanför Service Fabric (t. ex. en OS-tjänst)*: i det här fallet följer processen utanför Service Fabric även för CPU med befintliga tjänster. Lösningen på det här problemet är att konfigurera nodens kapacitet på rätt sätt för att redovisa OS-kostnader, som du ser i nästa avsnitt.

* *När begär Anden inte är lika med begränsningar*: enligt beskrivningen i RequestsAndLimits-exemplet ovan leder inte begär anden till att resurser på en nod reserveras. När en tjänst med gränser som är större än begär Anden placeras på en nod, kan den använda resurser (om de är tillgängliga) upp till dess gränser. I sådana fall kanske andra tjänster på noden inte kan använda resurser upp till sina begär ande värden.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Kluster konfiguration för att aktivera resurs styrning

När en nod startar och ansluter till klustret, identifierar Service Fabric mängden tillgängligt minne och det tillgängliga antalet kärnor och anger sedan nodens kapacitet för dessa två resurser.

För att lämna buffertutrymme för operativ systemet, och för andra processer som körs på noden, använder Service Fabric bara 80% av de tillgängliga resurserna på noden. Den här procent andelen kan konfigureras och kan ändras i kluster manifestet.

Här är ett exempel på hur du kan instruera Service Fabric att använda 50% av tillgänglig processor och 70% av tillgängligt minne:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

För de flesta kunder och scenarier är den automatiska identifieringen av nodens kapacitet för processor och minne den rekommenderade konfigurationen (automatisk identifiering är aktiverat som standard). Men om du behöver fullständig manuell konfigurering av nods kapacitet kan du konfigurera dem per nodtyp med hjälp av mekanismen för att beskriva noder i klustret. Här är ett exempel på hur du ställer in nodtypen med fyra kärnor och 2 GB minne:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

När automatisk identifiering av tillgängliga resurser är aktiverat och nodens kapacitet definieras manuellt i kluster manifestet, Service Fabric kontrollerar att noden har tillräckligt med resurser för att stödja den kapacitet som användaren har definierat:

* Om nodens kapaciteter som har definierats i manifestet är mindre än eller lika med de tillgängliga resurserna på noden, använder Service Fabric de kapaciteter som anges i manifestet.

* Om nodens kapaciteter som har definierats i manifestet är större än tillgängliga resurser, använder Service Fabric de tillgängliga resurserna som nodens kapacitet.

Automatisk identifiering av tillgängliga resurser kan stängas av om det inte behövs. Ändra följande inställning för att inaktivera den:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

För optimala prestanda bör följande inställning också aktive ras i kluster manifestet:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Från och med Service Fabric version 7,0 har vi uppdaterat regeln för hur resurs kapacitet för noder beräknas i de fall där användaren manuellt tillhandahåller värdena för resurs kapacitet. Vi tar hänsyn till följande scenario:
>
> * Det finns totalt 10 CPU-kärnor på noden
> * SF är konfigurerat för att använda 80% av det totala antalet resurser för användar tjänsterna (standardinställning), vilket lämnar en buffert på 20% för de andra tjänsterna som körs på noden (inklusive Service Fabric system tjänster)
> * Användaren bestämmer sig för att manuellt åsidosätta resurs kapaciteten för processor kärnor, och ställer in den på 5 kärnor
>
> Vi har ändrat regeln för hur den tillgängliga kapaciteten för Service Fabric användar tjänster beräknas på följande sätt:
>
> * Innan Service Fabric 7,0 beräknas tillgänglig kapacitet för användar tjänster till **5 kärnor** (kapacitets bufferten på 20% ignoreras)
> * Från och med Service Fabric 7,0 beräknas tillgänglig kapacitet för användar tjänster till **4 kärnor** (kapacitets bufferten på 20% ignoreras inte)

## <a name="specify-resource-governance"></a>Ange resurs styrning

Resurs styrnings förfrågningar och-gränser anges i program manifestet (service manifest import-avsnittet). Låt oss titta på några exempel:

**Exempel 1: RequestsOnly-specifikation**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

I det här exemplet `CpuCores` används attributet för att ange en begäran om 1 processor kärna för **ServicePackageA**. Eftersom processor gränsen ( `CpuCoresLimit` attribut) inte har angetts använder Service Fabric också det angivna begär ande värdet 1 kärna som processor gränsen för tjänst paketet.

**ServicePackageA** placeras bara på en nod där den återstående processor kapaciteten efter att ha dragit **summan av CPU-begäranden för alla tjänst paket som har placerats på noden** är större än eller lika med 1 kärna. På noden är tjänst paketet begränsat till en kärna. Tjänste paketet innehåller två kod paket (**CodeA1** och **CodeA2**) och båda anger `CpuShares` attributet. Förhållandet mellan CpuShares 512:256 används för att beräkna processor gränserna för de enskilda kod paketen. Därför kommer CodeA1 att begränsas till två tredjedelar av en kärna, och CodeA2 kommer att begränsas till en tredjedel av en kärna. Om CpuShares inte anges för alla kod paket kan Service Fabric dividera CPU-gränsen jämnt mellan dem.

Medan CpuShares som har angetts för kod paket representerar deras relativa andel av tjänst paketets övergripande processor gräns, anges minnes värden för kod paket i absoluta termer. I det här exemplet `MemoryInMB` används attributet för att ange minnes förfrågningar på 1024 MB för både CodeA1 och CodeA2. Eftersom minnes gränsen ( `MemoryInMBLimit` attribut) inte har angetts använder Service Fabric även de angivna värdena för begäran som begränsningar för kod paketen. Minnes förfrågan (och gränsen) för tjänst paketet beräknas som summan av värdena för minnes begär Ande (och gräns) för komponent kod paketen. För **ServicePackageA** beräknas dock minnes förfrågan och-gränsen som 2048 MB.

**ServicePackageA** placeras bara på en nod där den återstående minnes kapaciteten efter att ha dragit **summan av minnes förfrågningar för alla tjänst paket som har placerats på noden** är större än eller lika med 2048 MB. På noden är båda kod paketen begränsade till 1024 MB minne var. Kod paket (behållare eller processer) kan inte allokera mer minne än den här gränsen och om du försöker göra det uppstår minnes brist.

**Exempel 2: LimitsOnly-specifikation**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
I det här exemplet används `CpuCoresLimit` och används `MemoryInMBLimit` attribut som endast är tillgängliga i SF version 7,2 och senare. Attributet CpuCoresLimit används för att ange en processor gräns på 1 kärna för **ServicePackageA**. Eftersom processor förfrågan ( `CpuCores` attribut) inte har angetts anses den vara 0. `MemoryInMBLimit` attributet används för att ange minnes gränser på 1024 MB för CodeA1 och CodeA2 och eftersom begär Anden ( `MemoryInMB` attribut) inte har angetts anses de vara 0. Minnes förfrågan och-gränsen för **ServicePackageA** beräknas därför som 0 respektive 2048. Eftersom både processor-och minnes begär Anden för **ServicePackageA** är 0, visar den ingen belastning för CRM för placering, för `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` måtten och. Därför kan **ServicePackageA** placeras på valfri nod från ett resurs styrnings perspektiv **oavsett återstående kapacitet**. Till exempel 1, på noden, kommer CodeA1 att begränsas till två tredjedelar av en kärna och 1024 MB minne, och CodeA2 kommer att begränsas till en tredjedel av en kärna och 1024 MB minne.

**Exempel 3: RequestsAndLimits-specifikation**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
Genom att skapa de första två exemplen visar det här exemplet både förfrågningar och gränser för processor och minne. **ServicePackageA** har processor-och minnes förfrågningar på 1 kärna och 3072 (1024 + 2048) MB. Den kan bara placeras på en nod som har minst 1 kärna (och 3072 MB) kapacitet kvar efter att du subtraherat summan av alla processor-och minnes förfrågningar för alla tjänst paket som placeras på noden från nodens totala CPU (och minnes kapacitet). På noden begränsas CodeA1 till två tredjedelar av 2 kärnor och 3072 MB minne medan CodeA2 kommer att begränsas till en tredjedel av 2 kärnor och 4096 MB minne.

### <a name="using-application-parameters"></a>Använda program parametrar

När du anger resurs styrnings inställningar är det möjligt att använda [program parametrar](service-fabric-manage-multiple-environment-app-configuration.md) för att hantera flera AppData. I följande exempel visas användningen av program parametrarna:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

I det här exemplet anges standard parameter värden för produktions miljön, där varje tjänst paket får 4 kärnor och 2 GB minne. Det går att ändra standardvärden med program parameter-filer. I det här exemplet kan en parameter fil användas för att testa programmet lokalt, där det skulle få mindre resurser än i produktion:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Att ange resurs styrning med program parametrar är tillgängligt från och med Service Fabric version 6,1.<br>
>
> När program parametrar används för att ange resurs styrning kan Service Fabric inte nedgraderas till en tidigare version än version 6,1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Tvinga resurs begränsningar för användar tjänster

När du använder resurs styrning för Service Fabric tjänster garanterar det att de resursbaserade tjänsterna inte får överskrida sina resursers kvoter, men många användare behöver fortfarande köra vissa av deras Service Fabric tjänster i ett icke-styrd läge. När du använder unreglerade Service Fabric-tjänster är det möjligt att köra i situationer där "lediga" ej styrda tjänster förbrukar alla tillgängliga resurser på Service Fabric-noderna, vilket kan leda till allvarliga problem som:

* Resurs-effekter för andra tjänster som körs på noderna (inklusive Service Fabric system tjänster)
* Noder som slutar i ett ohälsosamt tillstånd
* Inga svar på Service Fabric kluster hanterings-API: er

För att förhindra att dessa situationer inträffar kan du med Service Fabric *upprätthålla resurs gränserna för alla Service Fabric användar tjänster som körs på noden* (både styrd och utan regler) för att garantera att användar tjänsterna aldrig kommer att använda mer än den angivna mängden resurser. Detta uppnås genom att ange värdet för EnforceUserServiceMetricCapacities-konfigurationen i avsnittet PlacementAndLoadBalancing i ClusterManifest till true. Den här inställningen är inaktive rad som standard.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Ytterligare anmärkningar:

* Tvingande resurs gräns gäller endast för `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` resurs mått
* Tillämpning av resurs begränsningen fungerar bara om nodens kapacitet för resurs måtten är tillgängligt för Service Fabric, antingen via mekanismen för automatisk identifiering eller genom att användare manuellt anger nodens kapacitet (enligt beskrivningen i [kluster konfigurationen för att aktivera avsnittet resurs styrning](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) ). Om nodens kapacitet inte har kon figurer ATS kan du inte använda funktionen för tvingande resurs gräns eftersom Service Fabric inte vet hur mycket resurser som ska reserveras för användar tjänster. Service Fabric kommer att utfärda en hälso varning om "EnforceUserServiceMetricCapacities" är sant men nodens kapacitet inte har kon figurer ATS.

## <a name="other-resources-for-containers"></a>Andra resurser för behållare

Förutom CPU och minne är det möjligt att ange andra resurs gränser för behållare. Dessa gränser anges på kod-paket nivå och tillämpas när behållaren startas. Till skillnad från CPU och minne är kluster resurs hanteraren inte medveten om dessa resurser och gör inga kapacitets kontroller eller belastnings utjämning för dem.

* *MemorySwapInMB*: mängden växlings minne som en behållare kan använda.
* *MemoryReservationInMB*: den mjuka gränsen för minnes styrning som endast tillämpas när minnes konkurrens identifieras på noden.
* *CpuPercent*: procent andelen CPU som containern kan använda. Om CPU-begäranden eller-gränser anges för tjänst paketet, ignoreras den här parametern i praktiken.
* *MaximumIOps*: högsta IOPS som en behållare kan använda (läsa och skriva).
* *MaximumIOBytesps*: den maximala IO (byte per sekund) som en behållare kan använda (läsa och skriva).
* *BlockIOWeight*: BLOCKets IO-vikt för relativa till andra behållare.

Dessa resurser kan kombineras med processor och minne. Här är ett exempel på hur du anger ytterligare resurser för behållare:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om kluster resurs hanteraren [i Introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Om du vill veta mer om program modellen, service paket och kod paket – och hur repliker mappar till dem, kan du läsa [en app i Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
