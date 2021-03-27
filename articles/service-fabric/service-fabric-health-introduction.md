---
title: Hälso övervakning i Service Fabric
description: En introduktion till övervaknings modellen för Azure Service Fabric Health som tillhandahåller övervakning av klustret och dess program och tjänster.
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: a1c545048739182e3baba3e3d94da1accca227d1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627423"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduktion till Service Fabric-hälsoövervakning
Azure Service Fabric introducerar en hälso modell som ger omfattande, flexibel och utöknings bar hälso utvärdering och rapportering. Modellen möjliggör real tids övervakning av klustrets tillstånd och de tjänster som körs i den. Du kan enkelt få hälso information och åtgärda eventuella problem innan de överlappar varandra och orsakar enorma avbrott. I den typiska modellen skickar tjänster rapporter baserat på deras lokala vyer och den informationen aggregeras för att ge en övergripande vy på kluster nivå.

Service Fabric-komponenter använder den här omfattande hälso modellen för att rapportera det aktuella tillståndet. Du kan använda samma mekanism för att rapportera hälso tillstånd från dina program. Om du investerar i hälso rapporter med hög kvalitet som samlar in dina anpassade villkor kan du enkelt identifiera och åtgärda problem för det program som körs.

> [!NOTE]
> Vi startade hälso under systemet för att åtgärda behovet av övervakade uppgraderingar. Service Fabric tillhandahåller övervakade program och kluster uppgraderingar som garanterar fullständig tillgänglighet, ingen stillestånds tid och minimal till ingen användar åtgärd. För att uppnå dessa mål kontrollerar uppgraderingen hälso tillståndet baserat på konfigurerade uppgraderings principer. En uppgradering kan bara fortsätta när hälso tillståndet respekterar önskade tröskelvärden. Annars återställs eller pausas uppgraderingen automatiskt för att ge administratörer möjlighet att åtgärda problemen. Mer information om program uppgraderingar finns i [den här artikeln](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Hälso Arkiv
Hälso lagringen skyddar hälso information om entiteter i klustret för enkel hämtning och utvärdering. Den implementeras som en Service Fabric bestående tillstånds känslig tjänst för att säkerställa hög tillgänglighet och skalbarhet. Hälso lagret är en del av **infrastruktur resursen:/systemet** och är tillgänglig när klustret är igång.

## <a name="health-entities-and-hierarchy"></a>Hälsoentiteter och hierarki
Hälsoentiteterna är ordnade i en logisk hierarki som samlar in interaktioner och beroenden mellan olika entiteter. Hälso lagringen skapar automatiskt hälsoentiteter och hierarkier baserat på rapporter som tas emot från Service Fabric-komponenter.

Hälso enheterna speglar Service Fabric entiteter. (Till exempel kan **hälsoprogram entiteten** matcha en program instans som distribueras i klustret, medan **hälsonodens entitet** matchar en Service Fabric klusternod.) Hälsohierarkin samlar in interaktioner för systementiteter och är grunden för avancerad hälso utvärdering. Du kan lära dig mer om viktiga Service Fabric koncept i [Service Fabric teknisk översikt](service-fabric-technical-overview.md). Mer information om program finns i [Service Fabric program modell](service-fabric-application-model.md).

Hälso deklarationerna och hierarkin gör det möjligt för kluster och program att rapporteras effektivt, felsökas och övervakas. Hälso modellen ger en korrekt, *detaljerad* representation av hälso tillståndet för de många flytt delarna i klustret.

![Hälsoentiteter.][1]
Hälsoentiteter, ordnade i en hierarki baserat på överordnade-underordnade relationer.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Hälso tillstånds enheterna är:

* **Kluster**. Representerar hälsan för ett Service Fabric kluster. Hälso rapporter i kluster beskriver villkor som påverkar hela klustret. Dessa villkor påverkar flera entiteter i klustret eller själva klustret. Utifrån villkoret kan rapportören inte begränsa problemet till en eller flera av de underordnade objekten. Exempel är hjärna i kluster delningen på grund av nätverks partitionering eller kommunikations problem.
* **Node**. Representerar hälsan för en Service Fabric nod. Hälso rapporter för noden beskriver villkor som påverkar nodens funktion. De påverkar normalt alla distribuerade entiteter som körs på den. Exempel innefattar nodens slut på disk utrymme (eller andra egenskaper för hela datorn, till exempel minne, anslutningar) och när en nod är nere. Noden entitet identifieras av nodnamnet (sträng).
* **Programmet**. Representerar hälsan för en program instans som körs i klustret. Program hälso rapporter beskriver villkor som påverkar programmets övergripande hälso tillstånd. De kan inte begränsas till enskilda underordnade (tjänster eller distribuerade program). Exempel på detta är interaktionen från slut punkt till slut punkt mellan olika tjänster i programmet. Programmets entitet identifieras av program namnet (URI).
* **Tjänsten**. Representerar hälsan för en tjänst som körs i klustret. Service Health-rapporter beskriver villkor som påverkar tjänstens övergripande hälso tillstånd. Rapportören kan inte begränsa problemet till en felaktig partition eller replik. Exempel omfattar en tjänst konfiguration (till exempel port eller extern fil resurs) som orsakar problem för alla partitioner. Tjänste entiteten identifieras av tjänst namnet (URI).
* **Partition**. Representerar hälsan för en tjänstmall. Partitioner hälso rapporter beskriver villkor som påverkar hela replik uppsättningen. Exempel på detta är när antalet repliker unders tiger mål antalet och när en partition har förlorat kvorum. Partitionens entitet identifieras av partitions-ID: t (GUID).
* **Replik**. Representerar hälsan för en tillstånds känslig tjänst replik eller en tillstånds lös tjänst instans. Repliken är den minsta enhet som övervaknings enheter och system komponenter kan rapportera om för ett program. För tillstånds känsliga tjänster inkluderar exempel en primär replik som inte kan replikera åtgärder till sekundära och långsamma replikeringar. Dessutom kan en tillstånds lös instans rapporteras när resurserna håller på att ta slut eller anslutnings problem. Replik entiteten identifieras av partitions-ID: t (GUID) och replik-eller instans-ID (Long).
* **DeployedApplication**. Representerar hälsan för ett *program som körs på en nod*. Distribuerade program hälso rapporter beskriver villkor som är specifika för programmet på noden som inte kan begränsas till tjänst paket som distribueras på samma nod. Exempel är fel när det inte går att hämta program paketet på noden och problem med att ställa in program säkerhets objekt på noden. Det distribuerade programmet identifieras med ett program namn (URI) och nodnamn (sträng).
* **DeployedServicePackage**. Representerar hälsan för ett tjänst paket som körs på en nod i klustret. Den beskriver villkor som är specifika för ett tjänst paket som inte påverkar de andra tjänst paketen på samma nod för samma program. Exempel inkluderar ett kod paket i tjänst paketet som inte kan startas och ett konfigurations paket som inte kan läsas. Det distribuerade tjänst paketet identifieras av program namn (URI), nodnamn (sträng), tjänst manifest namn (sträng) och aktiverings-ID för tjänst paket (sträng).

Hälso modellens kornig het gör det enkelt att identifiera och åtgärda problem. Om en tjänst till exempel inte svarar är det möjligt att rapportera att program instansen är i fel tillstånd. Rapportering på den nivån är inte idealiskt, men eftersom problemet kanske inte påverkar alla tjänster i programmet. Rapporten bör tillämpas på den felaktiga tjänsten eller en speciell underordnad partition, om mer information pekar på den partitionen. Data placeras automatiskt i-hierarkin och en felaktig partition görs synlig på tjänst-och program nivå. Den här agg regeringen hjälper till att hitta och lösa rotor saken till problemet snabbare.

Certifikathierarkin består av överordnade och underordnade relationer. Ett kluster består av noder och program. Program har tjänster och distribuerade program. Distribuerade program har distribuerade tjänst paket. Tjänsterna har partitioner och varje partition har en eller flera repliker. Det finns en särskild relation mellan noder och distribuerade entiteter. En felaktig nod som rapporteras av dess system komponent, tjänsten Redundanshanteraren, påverkar de distribuerade program, tjänst paket och repliker som distribueras på den.

Hälsohierarkin representerar det senaste tillståndet för systemet baserat på de senaste hälso rapporterna, vilket är nästan real tids information.
Interna och externa övervaknings enheter kan rapportera om samma entiteter baserat på programspecifik logik eller anpassade övervakade villkor. Användar rapporter är tillsammans med system rapporterna.

Planera för att investera i hur du ska rapportera och reagera på hälsan under utformningen av en stor moln tjänst. Den här första investeringen gör tjänsten lättare att felsöka, övervaka och använda.

## <a name="health-states"></a>Hälso tillstånd
Service Fabric använder tre hälso tillstånd för att beskriva om en entitet är felfri eller inte: OK, varning och fel. Alla rapporter som skickas till hälso arkivet måste ange något av dessa tillstånd. Resultatet av hälso utvärderingen är ett av dessa tillstånd.

Möjliga [hälso tillstånd](/dotnet/api/system.fabric.health.healthstate) är:

* **OK**. Entiteten är felfri. Inga kända problem har rapporter ATS för den eller dess underordnade (om tillämpligt).
* **Varning**. Entiteten innehåller vissa problem, men den kan fortfarande fungera korrekt. Det finns till exempel fördröjningar, men de orsakar inga funktionella problem än. I vissa fall kan varnings villkoret korrigeras utan extern åtgärd. I dessa fall kan hälso rapporter höja medvetenheten och ge insyn i vad som händer. I andra fall kan varnings villkoret försämras till ett allvarligt problem utan åtgärder från användaren.
* **Fel**. Enheten är inte felfri. Åtgärden bör vidtas för att åtgärda status för entiteten, eftersom den inte kan fungera korrekt.
* **Okänt**. Enheten finns inte i hälso arkivet. Det här resultatet kan hämtas från de distribuerade frågor som sammanfogar resultat från flera komponenter. Till exempel går frågan Hämta lista över noder till **FailoverManager**, **ClusterManager** och **HealthManager**; Hämta program lista fråga går till **ClusterManager** och **HealthManager**. Dessa frågor sammankopplar resultat från flera system komponenter. Om en annan system komponent returnerar en entitet som inte finns i Health Store har det sammanslagna resultatet okänt hälso tillstånd. En entitet är inte i arkivet eftersom hälso rapporter ännu inte har bearbetats eller om entiteten har rensats efter borttagning.

## <a name="health-policies"></a>Hälso principer
Hälso lagret tillämpar hälso principer för att avgöra om en entitet är felfri baserat på dess rapporter och dess underordnade.

> [!NOTE]
> Hälso principer kan anges i kluster manifestet (för kluster-och Node-utvärdering av hälsa) eller i applikations manifestet (för program utvärdering och alla dess underordnade). Hälso utvärderings begär Anden kan också skicka i anpassade hälso utvärderings principer som endast används för den utvärderingen.
> 
> 

Som standard tillämpar Service Fabric strikta regler (allting måste vara felfria) för den överordnade och underordnade hierarkiska relationen. Om även en av de underordnade objekten har en händelse som inte är felfri betraktas den överordnade aktiviteten som ohälsosam.

### <a name="cluster-health-policy"></a>Kluster hälso princip
[Kluster hälso principen](/dotnet/api/system.fabric.health.clusterhealthpolicy) används för att utvärdera hälso tillståndet för klustret och nodens hälso tillstånd. Principen kan definieras i kluster manifestet. Om den inte finns används standard principen (noll tolererade Miss lyckas).

Kluster hälso principen innehåller:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om varnings hälso rapporter ska behandlas som fel under hälso utvärderingen. Standard: falskt.
* [MaxPercentUnhealthyApplications](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Anger den maximala procent andelen program som kan vara felfria innan klustret betraktas som ett fel.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Anger den maximala procent andelen av de noder som kan vara felfria innan klustret betraktas som ett fel. I stora kluster är vissa noder alltid nere eller ut för reparationer, så den här procent andelen bör konfigureras för att tolerera.
* [ApplicationTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Princip mappningen för program typens hälso princip kan användas vid utvärdering av kluster hälsa för att beskriva särskilda program typer. Som standard placeras alla program i en pool och utvärderas med MaxPercentUnhealthyApplications. Om vissa program typer ska behandlas annorlunda kan de tas bort från den globala poolen. I stället utvärderas de mot procent andelen som är kopplade till deras program typs namn i kartan. I ett kluster finns det till exempel tusentals program av olika typer och några kontroll program instanser av en särskild program typ. Kontroll programmen ska aldrig ha fel. Du kan ange globala MaxPercentUnhealthyApplications till 20% för att tolerera vissa problem, men för program typen "ControlApplicationType" anger du MaxPercentUnhealthyApplications till 0. På så sätt kommer klustret att utvärderas som varning om några av de många programmen inte är felfria, men lägre än den globala procent andelen. En varnings hälso tillstånd påverkar inte kluster uppgraderingen eller annan övervakning som utlöses av fel hälso tillstånd. Men till och med ett kontroll program i fel skulle klustret vara skadat, vilket utlöser återställnings-eller pausar kluster uppgraderingen, beroende på uppgraderings konfigurationen.
  För de program typer som definierats i kartan tas alla program instanser bort från den globala poolen med program. De utvärderas baserat på det totala antalet program av program typen, med hjälp av den specifika MaxPercentUnhealthyApplications från kartan. Alla resten av programmen finns kvar i den globala poolen och utvärderas med MaxPercentUnhealthyApplications.

  Följande exempel är ett utdrag från ett kluster manifest. Om du vill definiera poster i program typs mappningen, anger du parameter namnet med "ApplicationTypeMaxPercentUnhealthyApplications-", följt av programmets typ namn.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
    </Section>
  </FabricSettings>
  ```

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). Typ av hälso princip för nodtyp kan användas under utvärdering av kluster hälsa för att beskriva särskilda nodtyper. Nodtypen utvärderas mot procent andelen som är kopplade till deras nodnamn i kartan. Att ange det här värdet har ingen påverkan på den globala poolen med noder som används för `MaxPercentUnhealthyNodes` . Ett kluster har till exempel hundratals noder av olika typer och några nodtyper som är värdar för viktiga arbeten. Inga noder i den typen bör vara nere. Du kan ange globala `MaxPercentUnhealthyNodes` till 20% för att tolerera vissa problem för alla noder, men för nodtypen `SpecialNodeType` anger `MaxPercentUnhealthyNodes` du till 0. På så sätt kommer klustret att utvärderas som varnings hälso tillstånd om några av de många noderna inte är felfria men lägre än den globala nivån i procent. En varnings hälso tillstånd påverkar inte kluster uppgraderingen eller också utlöses den andra övervakningen av ett fel hälso tillstånd. Men även en nod av typen `SpecialNodeType` i ett fel hälso tillstånd gör att klustret inte är felfritt och utlöser återställningen eller pausar kluster uppgraderingen, beroende på uppgraderings konfigurationen. Om du ställer in globala `MaxPercentUnhealthyNodes` till 0 och anger `SpecialNodeType` Max procent Felaktiga noder till 100 med en nod av typen `SpecialNodeType` i ett fel tillstånd, kommer klustret fortfarande att placeras i ett fel tillstånd eftersom den globala begränsningen är mer strikt i det här fallet. 

  Följande exempel är ett utdrag från ett kluster manifest. Om du vill definiera poster i mappningen av nodtypen anger du parameter namnet med "NodeTypeMaxPercentUnhealthyNodes-", följt av namnet på nodtypen.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Program hälso princip
[Program hälso principen](/dotnet/api/system.fabric.health.applicationhealthpolicy) beskriver hur utvärderingen av händelser och sammansättning av underordnade tillstånd görs för program och deras underordnade. Den kan definieras i applikations manifestet **ApplicationManifest.xml** i programpaketet. Om inga principer anges förutsätter Service Fabric att enheten inte är felfri om den har en hälso rapport eller en underordnad i varnings-eller fel hälso tillståndet.
De konfigurerbara principerna är:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Anger om varnings hälso rapporter ska behandlas som fel under hälso utvärderingen. Standard: falskt.
* [MaxPercentUnhealthyDeployedApplications](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Anger den maximala procent andelen av distribuerade program som kan vara felfria innan programmet betraktas som ett fel. Den här procent satsen beräknas genom att dividera antalet ej hälsodistribuerade program över antalet noder som programmen för närvarande har distribuerat i klustret. Beräkningen avrundar upp till att tolerera ett problem på ett litet antal noder. Standard procents ATS: noll.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Anger standard hälso principen för tjänst typen, som ersätter standard hälso principen för alla tjänst typer i programmet.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Innehåller en karta över tjänst hälso principer per tjänst typ. Dessa principer ersätter standard hälso principerna för tjänst typen för varje angiven tjänst typ. Om ett program till exempel har en tillstånds lös tjänst typ för gateway och en tillstånds känslig motor tjänst typ, kan du konfigurera hälso principerna för utvärderingen på olika sätt. När du anger princip per tjänst typ kan du få mer detaljerad kontroll över hälso tillståndet för tjänsten.

### <a name="service-type-health-policy"></a>Hälso princip för tjänst typ
[Hälso principen för tjänst typen](/dotnet/api/system.fabric.health.servicetypehealthpolicy) anger hur du ska utvärdera och aggregera tjänsterna och underordnade tjänster. Principen innehåller:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Anger den maximala procent andelen av felaktiga partitioner innan en tjänst betraktas som ohälsosam. Standard procents ATS: noll.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Anger den maximala procent andelen av felaktiga repliker innan en partition anses vara ohälsosam. Standard procents ATS: noll.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Anger den maximala procent andelen av felaktiga tjänster innan programmet betraktas som ohälsosamt. Standard procents ATS: noll.

Följande exempel är ett utdrag från ett program manifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Hälso utvärdering
Användare och automatiserade tjänster kan när som helst utvärdera hälso tillståndet för alla enheter. För att utvärdera hälso tillståndet för en entitet aggregerar hälso lagret alla hälso rapporter i entiteten och utvärderar alla dess underordnade (om tillämpligt). Algoritmen för hälso tillstånds insamling använder hälso principer som anger hur du ska utvärdera hälso rapporter och hur du sammanställer underordnade hälso tillstånd (om tillämpligt).

### <a name="health-report-aggregation"></a>Insamling av hälso rapporter
En entitet kan ha flera hälso rapporter som skickas av olika rapporter (system komponenter eller övervaknings enheter) i olika egenskaper. Sammanställningen använder de tillhör ande hälso principerna, särskilt ConsiderWarningAsError-medlemmen i program-eller kluster hälso principen. ConsiderWarningAsError anger hur varningar ska utvärderas.

Det sammanställda hälso tillståndet utlöses av de *värsta* hälso rapporterna på entiteten. Om det finns minst en fel hälso rapport, är det sammanlagda hälso tillståndet ett fel.

![Insamling av hälso rapporter med fel rapport.][2]

En hälsoentitet som har en eller flera fel hälso rapporter utvärderas som ett fel. Samma sak gäller för en hälso rapport som har gått ut, oavsett hälso tillstånd.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Om det inte finns några fel rapporter och en eller flera varningar, är det sammanställda hälso tillståndet antingen varnings-eller fel, beroende på princip flaggan ConsiderWarningAsError.

![Insamling av hälso rapporter med varnings rapport och ConsiderWarningAsError false.][3]

Insamling av hälso rapporter med varnings rapport och ConsiderWarningAsError inställt på false (standard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Underordnad hälso agg regering
Det sammanlagda hälso tillståndet för en entitet motsvarar de underordnade hälso tillstånden (om tillämpligt). Algoritmen för att aggregera underordnade hälso tillstånd använder hälso principerna som är tillämpliga baserat på enhets typen.

![Hälso agg regering för underordnade entiteter.][4]

Underordnad agg regering baserat på hälso principer.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

När hälso insamlingen har utvärderat alla underordnade, aggregerar de sina hälso tillstånd baserat på den konfigurerade maximala procent andelen Felaktiga underordnade. Den här procent andelen hämtas från principen som baseras på entiteten och den underordnade typen.

* Om alla underordnade har statusen OK är det underordnade sammanställda hälso tillståndet OK.
* Om underordnade har både OK och varnings tillstånd, är det underordnade sammanställda hälso tillståndet varning.
* Om det finns underordnade med fel tillstånd som inte respekterar den högsta tillåtna procent andelen av ohälsosama underordnade hälso tillstånd, är det sammanställda överordnade hälso tillståndet ett fel.
* Om underordnade med fel tillstånd respekterar den högsta tillåtna procent andelen av ohälsosama underordnade hälso tillstånd, är det sammanställda överordnade hälso tillståndet varning.

## <a name="health-reporting"></a>Hälso rapportering
System komponenter, system Fabric-program och interna/externa övervaknings enheter kan rapportera mot Service Fabric entiteter. Rapporterna gör *lokala* bestämningar av hälsan hos de övervakade enheterna, baserat på de villkor som de övervakar. De behöver inte titta på några globala eller aggregerade data. Det önskade beteendet är att ha enkla rapporter och inte komplexa organismer som behöver titta på många saker för att härleda vilken information som ska skickas.

För att skicka hälso data till hälso lagret måste en rapportör identifiera den berörda enheten och skapa en hälso rapport. Om du vill skicka rapporten använder du [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, rapportera hälso-API: er som exponeras på `Partition` `CodePackageActivationContext` objekten eller, PowerShell-cmdlets eller rest.

### <a name="health-reports"></a>Hälso rapporter
[Hälso rapporter](/dotnet/api/system.fabric.health.healthreport) för var och en av entiteterna i klustret innehåller följande information:

* **SourceId**. En sträng som unikt identifierar rapportören för hälso händelsen.
* **Enhets identifierare**. Identifierar den entitet där rapporten används. Det skiljer sig beroende på [enhets typen](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Flernodskluster. Inga.
  * Nodfel. Nodnamn (sträng).
  * Applicering. Program namn (URI). Representerar namnet på den program instans som distribueras i klustret.
  * Telefonitjänstprovider. Tjänst namn (URI). Representerar namnet på den tjänst instans som distribueras i klustret.
  * Partitionstabellen. Partitions-ID (GUID). Representerar Partitionens unika identifierare.
  * Replica. Det tillstånds känsliga tjänstens replik-ID eller det tillstånds lösa tjänst instans-ID: t (INT64).
  * DeployedApplication. Program namn (URI) och nodnamn (sträng).
  * DeployedServicePackage. Program namn (URI), nodnamn (sträng) och tjänst manifest namn (sträng).
* **Egenskap**. En *sträng* (inte en fast uppräkning) som tillåter rapportören att kategorisera hälso händelsen för en viss egenskap i entiteten. Rapportör A kan till exempel rapportera hälso tillståndet för Node01 "lagring"-egenskapen och rapportör B kan rapportera hälsan för egenskapen Node01 "Connectivity". I hälso lagret behandlas dessa rapporter som separata hälso händelser för Node01-enheten.
* **Beskrivning**. En sträng som tillåter en rapportör att tillhandahålla detaljerad information om hälso händelsen. **SourceId**, **Property** och **hälsohälso** tillstånd bör beskriva rapporten fullständigt. Beskrivningen lägger till läsbar information om rapporten. Texten gör det enklare för administratörer och användare att förstå hälso rapporten.
* **Hälso** tillstånd. En [uppräkning](service-fabric-health-introduction.md#health-states) som beskriver rapportens hälso tillstånd. Godkända värden är OK, varning och fel.
* **TimeToLive**. TimeSpan som anger hur länge hälso rapporten är giltig. Tillsammans med **RemoveWhenExpired** kan hälso lagret veta hur du kan utvärdera utgångna händelser. Som standard är värdet oändligt och rapporten är giltig för alltid.
* **RemoveWhenExpired**. Ett booleskt värde. Om det är inställt på Sant tas den utgångna hälso rapporten bort automatiskt från hälso lagret och rapporten påverkar inte utvärderingen av enhetens hälsa. Används endast när rapporten är giltig under en angiven tids period, och rapportören behöver inte uttryckligen ta bort den. Den används också för att ta bort rapporter från hälso lagret (till exempel en övervaknings enhet ändras och slutar skicka rapporter med föregående källa och egenskap). Den kan skicka en rapport med en kort TimeToLive tillsammans med RemoveWhenExpired för att ta bort alla tidigare tillstånd från hälso lagret. Om värdet är inställt på falskt behandlas den utgångna rapporten som ett fel i hälso utvärderingen. Det falska värdet signalerar till hälso arkivet som källan bör rapportera regelbundet om den här egenskapen. Om den inte gör det måste det vara något fel med övervaknings enheten. Hälso tillståndet för övervaknings enheten fångas genom att överväger händelsen som ett fel.
* **SequenceNumber**. Ett positivt heltal som behöver utökas, motsvarar den ordningen på rapporterna. Den används av Health Store för att identifiera inaktuella rapporter som tas emot sent på grund av nätverks fördröjningar eller andra problem. En rapport avvisas om sekvensnumret är mindre än eller lika med det senast använda talet för samma entitet, källa och egenskap. Om inget värde anges genereras sekvensnumret automatiskt. Det är nödvändigt att endast placeras i sekvensnumret vid rapportering av tillstånds över gångar. I den här situationen behöver källan komma ihåg vilka rapporter den skickade och behålla informationen för återställning vid redundans.

Dessa fyra delar av information--SourceId, enhets identifierare, egenskap och hälso tillstånd – krävs för varje hälso rapport. Det går inte att starta en SourceId-sträng med prefixet **system.**, som är reserverat för system rapporter. För samma entitet finns det bara en rapport för samma källa och egenskap. Flera rapporter för samma källa och egenskap åsidosätter varandra, antingen på hälso klient sidan (om de är grupperade) eller på hälso Arkiv sidan. Ersättningen baseras på serie nummer. nyare rapporter (med högre ordnings nummer) ersätter äldre rapporter.

### <a name="health-events"></a>Hälso händelser
Internt bevarar hälso [tillståndet hälso händelser](/dotnet/api/system.fabric.health.healthevent)som innehåller all information från rapporterna och ytterligare metadata. Metadata innehåller den tid som rapporten fick till hälso klienten och den tid som den ändrades på Server sidan. Hälso tillstånds händelser returneras av [hälso frågor](service-fabric-view-entities-aggregated-health.md#health-queries).

De tillagda metadatana innehåller:

* **SourceUtcTimestamp**. Tiden då rapporten gavs till hälso klienten (Coordinated Universal Time).
* **LastModifiedUtcTimestamp**. Tiden då rapporten senast ändrades på Server sidan (Coordinated Universal Time).
* **IsExpired**. En flagga som anger om rapporten har upphört att gälla när frågan kördes av hälso lagret. En händelse kan bara ha upphört om RemoveWhenExpired är false. Annars returneras inte händelsen av frågan och tas bort från butiken.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Senaste gången för OK/varning/fel över gångar. Dessa fält ger historiken för hälso tillstånds över gångar för händelsen.

Du kan använda fält för tillstånds över gång för aviseringar med smartare eller historisk hälso information. De möjliggör scenarier som:

* Avisera när en egenskap har varit vid varning/fel i mer än X minuter. Genom att kontrol lera villkoret under en tids period undviks aviseringar på tillfälliga villkor. Till exempel, en avisering om hälso tillståndet har varit varning under mer än fem minuter kan översättas till (hälso deklaration = = varning och nu-LastWarningTransitionTime > 5 minuter).
* Varna bara för villkor som har ändrats under de senaste X minuterna. Om en rapport redan har varit vid ett fel före den angivna tiden kan den ignoreras eftersom den redan signalerades tidigare.
* Om en egenskap växlas mellan varning och fel, fastställer du hur lång tid det har varit skadat (det vill säga inte OK). En avisering om egenskapen inte har varit felfri under mer än fem minuter kan översättas till (hälsohälsa! = OK och nu-LastOkTransitionTime > 5 minuter).

## <a name="example-report-and-evaluate-application-health"></a>Exempel: rapportera och utvärdera program hälsa
I följande exempel skickas en hälso rapport via PowerShell i Application **Fabric:/WORDCOUNT** från källan min **övervaknings** enhet. Hälso rapporten innehåller information om hälso egenskapen "tillgänglighet" i ett fel hälso tillstånd med oändlig TimeToLive. Sedan frågar den program hälsan, som returnerar sammanställda hälso tillstånds fel och rapporterade hälso händelser i listan över hälso händelser.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Användning av hälso modell
Hälso modellen gör det möjligt för moln tjänster och den underliggande Service Fabrics plattformen att skalas, eftersom övervakning och hälso bestämning distribueras bland de olika övervakarna i klustret.
Andra system har en enda, centraliserad tjänst på kluster nivå som analyserar all *potentiellt* användbar information som skickas av tjänster. Den här metoden hindrar deras skalbarhet. Det innebär inte heller att de kan samla in information som kan hjälpa till att identifiera problem och potentiella problem så nära rotor saken som möjligt.

Hälso modellen används kraftigt för övervakning och diagnostik för att utvärdera kluster-och program hälsa och för övervakade uppgraderingar. Andra tjänster använder hälso data för att utföra automatiska reparationer, skapa kluster hälso historik och skicka aviseringar på vissa villkor.

## <a name="next-steps"></a>Nästa steg
[Visa Service Fabric hälso rapporter](service-fabric-view-entities-aggregated-health.md)

[Använda system hälso rapporter för fel sökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Rapportera och kontrol lera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Lägg till anpassade Service Fabric hälso rapporter](service-fabric-report-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric program uppgradering](service-fabric-application-upgrade.md)
