---
title: Överväganden vid planering av Service Fabric kluster kapacitet
description: Nodtyper, hållbarhet, tillförlitlighet och andra saker att tänka på när du planerar Service Fabric-klustret.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: b3361337bb0cf60e47efe198aad7aa8cc20ae7b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714943"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Överväganden vid planering av Service Fabric kluster kapacitet

Planering av kluster kapacitet är viktigt för varje Service Fabric produktions miljö. Viktiga överväganden är:

* **Ursprungligt antal och egenskaper för *klusternoder***

* ***Hållbarhets* nivå för varje nodtyp** som avgör Service Fabric VM-behörigheter i Azure-infrastrukturen

* **Klustrets *Tillförlitlighets* nivå**, vilket avgör stabiliteten för Service Fabric system tjänster och övergripande kluster funktion

Den här artikeln vägleder dig genom de viktigaste besluts punkterna för vart och ett av dessa områden.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Ursprungligt antal och egenskaper för klusternoder

En *nodtyp* definierar storlek, antal och egenskaper för en uppsättning noder (virtuella datorer) i klustret. Varje nodtyp som definieras i ett Service Fabric-kluster mappar till en [skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/overview.md).

Eftersom varje nodtyp är en distinkt skalnings uppsättning, kan den skalas upp eller ned oberoende av varandra, ha olika uppsättningar portar öppna och ha olika kapacitets mått. Mer information om relationen mellan nodtyper och skalnings uppsättningar för virtuella datorer finns i [Service Fabric typer av klusternoder](service-fabric-cluster-nodetypes.md).

Varje kluster kräver en **primär nodtyp**, som kör kritiska system tjänster som tillhandahåller Service Fabric plattforms funktioner. Även om det är möjligt att även använda primära nodtyper för att köra dina program, rekommenderar vi att du bara dedikerar dem att köra system tjänster.

**Typer av icke-primära noder** kan användas för att definiera program roller (till exempel *frontend-* och *backend-* tjänster) och för att fysiskt isolera tjänster i ett kluster. Service Fabric kluster kan ha noll eller flera icke-primära nodtyper.

Den primära nodtypen konfigureras med hjälp av `isPrimary` attributet under nodtypen definition i mallen för Azure Resource Manager distribution. Se [NodeTypeDescription-objektet](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) för en fullständig lista över egenskaper för nodtyp. Du kan till exempel öppna en *AzureDeploy.jspå* en fil i [Service Fabric kluster exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) och söka efter objektet *på sidan* `nodeTypes` .

### <a name="node-type-planning-considerations"></a>Planerings överväganden för Node-typ

Antalet typer av inledande noder beror på syftet med ditt kluster och de program och tjänster som körs på den. Överväg följande frågor:

* ***Har ditt program flera tjänster och behöver de vara offentliga eller Internet riktade?***

    Typiska program innehåller en frontend Gateway-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med frontend-tjänsterna, med separata nätverk mellan frontend-och backend-tjänsterna. Dessa fall kräver vanligt vis tre nodtyper: en primär nodtyp och två typer av icke-primära noder (var och en för frontend-och backend-tjänsten).

* ***Har de tjänster som utgör ditt program olika infrastruktur behov, till exempel större RAM-minne eller högre CPU-cykler?***

    Ofta kan frontend-tjänsten köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna för Internet.  Beräknings intensiva Server dels tjänster kan behöva köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är Internet-riktade. Genom att definiera olika nodtyper för dessa tjänster kan du göra mer effektiv och säker användning av underliggande Service Fabric virtuella datorer och göra det möjligt för dem att skala dem oberoende av varandra. Mer information om hur du uppskattar hur mycket resurser du behöver finns i [kapacitets planering för Service Fabric program](service-fabric-capacity-planning.md)

* ***Måste alla dina program tjänster skala ut bortom 100 noder?***

    En enskild nodtyp kan inte på ett tillförlitligt sätt skala bortom 100 noder per skalnings uppsättning för virtuella datorer för Service Fabric program. Om du kör fler än 100 noder krävs ytterligare skalnings uppsättningar för virtuella datorer (och därför ytterligare nodtyper).

* ***Kommer klustret att sträcka sig över Tillgänglighetszoner?***

    Service Fabric stöder kluster som sträcker sig över flera [Tillgänglighetszoner](../availability-zones/az-overview.md) genom att distribuera nodtyper som är fästa på vissa zoner och som garanterar hög tillgänglighet för dina program. Tillgänglighetszoner kräver ytterligare planering och minimi krav för Node-typ. Mer information finns i [Rekommenderad topologi för primär nodtyp för Service Fabric kluster som sträcker sig över Tillgänglighetszoner](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

När du bestämmer antalet och egenskaperna för nodtyper för den första skapandet av klustret, bör du tänka på att du alltid kan lägga till, ändra eller ta bort nodtyper (icke-primära) när klustret har distribuerats. [Primära nodtyper kan också ändras](service-fabric-scale-up-primary-node-type.md) i kluster som körs (även om sådana åtgärder kräver stor planering och försiktighet i produktions miljöer).

Ytterligare överväganden för egenskaperna för nodtypen är en hållbarhets nivå, som avgör vilken typ av nodtyp som de virtuella datorerna har i Azure-infrastrukturen. Använd storleken på de virtuella datorer du väljer för klustret och antalet instanser som du tilldelar för enskilda nodtyper för att fastställa lämplig hållbarhets nivå för var och en av dina nodtyper, enligt beskrivningen härnäst.

## <a name="durability-characteristics-of-the-cluster"></a>Hållbarhets egenskaper för klustret

*Hållbarhets nivån* anger de behörigheter som dina Service Fabric virtuella datorer har med den underliggande Azure-infrastrukturen. Med den här behörigheten kan Service Fabric pausa eventuell infrastruktur förfrågan på VM-nivå (till exempel omstart, avbildning eller migrering) som påverkar kraven för kvorum för Service Fabric system tjänster och dina tillstånds känsliga tjänster.

> [!IMPORTANT]
> Hållbarhets nivån har angetts per nodtyp. Om ingen anges används *brons* nivån, men den ger inte automatiska uppgraderingar av operativ systemet. *Silver* -eller *Gold* -hållbarhet rekommenderas för produktions arbets belastningar.

I tabellen nedan visas Service Fabric hållbarhets nivåer, deras krav och affordances.

| Hållbarhets nivå  | Minsta antal virtuella datorer som krävs | VM-storlekar som stöds                                                                  | Uppdateringar som du gör i skalnings uppsättningen för den virtuella datorn                               | Uppdateringar och underhåll som har startats av Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Guld             | 5                              | Full-Node-storlekar som är dedikerade till en enskild kund (till exempel L32s, GS5, G5, DS15_v2 D15_v2) | Kan förskjutas tills de godkänts av Service Fabrics klustret | Kan pausas i 2 timmar per uppgraderings domän för att tillåta ytterligare tid för repliker att återställas från tidigare fel |
| Silver           | 5                              | Virtuella datorer med en enda kärna eller över minst 50 GB lokal SSD                      | Kan förskjutas tills de godkänts av Service Fabrics klustret | Kan inte fördröjas under en viss tids period                                                    |
| Brons          | 1                              | Virtuella datorer med minst 50 GB lokal SSD                                              | Kommer inte att fördröjas av Service Fabric-klustret           | Kan inte fördröjas under en viss tids period                                                    |

> [!NOTE]
> Ovannämnda lägsta antal virtuella datorer är ett nödvändigt krav för varje hållbarhets nivå. Vi har gjort en validering på plats som gör det möjligt att skapa eller ändra befintliga scalesets för virtuella datorer som inte uppfyller dessa krav.

> [!WARNING]
> Med brons hållbarhet är automatisk uppgradering av OS-avbildningen inte tillgänglig. Även om [program för uppdaterings dirigering](service-fabric-patch-orchestration-application.md) (endast avsett för icke-Azure-värdbaserade kluster) *inte rekommenderas* för silver eller större hållbarhets nivåer, är det bara ditt alternativ att automatisera Windows-uppdateringar med avseende på Service Fabric uppgraderings domäner.

> [!IMPORTANT]
> Oberoende av hållbarhets nivån förstörs klustret om du kör en [avfördelnings](/rest/api/compute/virtualmachinescalesets/deallocate) åtgärd på en skalnings uppsättning för virtuell dator.

### <a name="bronze"></a>Brons

Nodtyper som kör med brons hållbarhet får inga privilegier. Det innebär att infrastruktur jobb som påverkar dina tillstånds känsliga arbets belastningar inte stoppas eller fördröjs. Använd brons hållbarhet för nodtyper som bara kör tillstånds lösa arbets belastningar. För produktions arbets belastningar rekommenderas att du kör silver eller högre.

### <a name="silver-and-gold"></a>Silver och guld

Använd silver eller guld tålighet för alla nodtyper som är värdar för tillstånds känsliga tjänster som du förväntar dig att skala in ofta, och där du vill att distributions åtgärder ska fördröjas och kapacitet för att förenkla processen. Skalnings scenarier ska inte påverka ditt val av hållbarhets nivå.

#### <a name="advantages"></a>Fördelar

* Minskar antalet nödvändiga steg för skalnings åtgärder (Node-inaktive ring och Remove-ServiceFabricNodeState anropas automatiskt).
* Minskar risken för data förlust på grund av ändringar i VM-storlek och åtgärder för Azure-infrastrukturen på plats.

#### <a name="disadvantages"></a>Nackdelar

* Distributioner till skalnings uppsättningar för virtuella datorer och andra relaterade Azure-resurser kan ta lång tid, vara fördröjda eller blockeras helt av problem i klustret eller på infrastruktur nivå.
* Ökar antalet händelser för [replik livs cykeln](service-fabric-reliable-services-lifecycle.md) (till exempel primära växlingar) på grund av automatiserade nod-inaktivitet under Azures infrastruktur åtgärder.
* Tar noder ur drift under tids perioder medan Azure-plattformens program uppdateringar eller aktiviteter för maskin varu underhåll inträffar. Du kan se noder med status inaktive rad eller inaktive rad under dessa aktiviteter. Detta minskar kapaciteten för klustret tillfälligt, men påverkar inte tillgängligheten för ditt kluster eller program.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Metod tips för silver-och guld-typer av hållbarhets tid

Följ dessa rekommendationer för att hantera nodtyper med silver eller guld tålighet:

* Se till att ditt kluster och program är felfritt och se till att programmen svarar på alla [livs cykel händelser för tjänste repliken](service-fabric-reliable-services-lifecycle.md) (t. ex. replik i build är fastnat) inom rimlig tid.
* Använd säkrare sätt att göra en ändring av storleken på virtuella datorer (skala upp/ned). Att ändra storleken på den virtuella datorns skalnings uppsättning kräver noggrann planering och försiktighet. Mer information finns i [skala upp en Service Fabric nodtyp](service-fabric-scale-up-primary-node-type.md)
* Behåll det lägsta antalet fem noder för alla skalnings uppsättningar för virtuella datorer som har en hög grad av guld eller silver aktiverat. Klustret kommer att ange fel tillstånd om du skalar under det här tröskelvärdet och du måste manuellt rensa tillstånd ( `Remove-ServiceFabricNodeState` ) för de borttagna noderna.
* Varje skalnings uppsättning för virtuella datorer med silver eller guld måste mappas till en egen nodtyp i Service Fabric klustret. Om du mappar flera skalnings uppsättningar för virtuella datorer till en enda nodtyp förhindrar det att samordningen mellan Service Fabric-klustret och Azure-infrastrukturen fungerar som de ska.
* Ta inte bort slumpmässiga VM-instanser, Använd alltid skalnings uppsättning för virtuella datorer i funktionen. Borttagning av slumpmässiga VM-instanser har möjlighet att skapa obalanser i den virtuella dator instansen sprids över [uppgraderings domäner](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) och [fel domäner](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Den här obalansen kan negativt påverka systemets möjlighet att på ett korrekt sätt belastningsutjämna mellan tjänst instanserna/tjänst replikerna.
* Om du använder autoskalning ställer du in reglerna så att skala i (borttagning av VM-instanser) bara utförs en nod i taget. Att skala ned fler än en instans i taget är inte säkert.
* Om du tar bort eller frigör virtuella datorer på den primära nodtypen, minskar du aldrig antalet allokerade virtuella datorer under vad Tillförlitlighets nivån kräver. De här åtgärderna kommer att blockeras oändligt i en skalnings uppsättning med en hållbarhets nivå på silver eller guld.

### <a name="changing-durability-levels"></a>Ändra hållbarhets nivåer

Inom vissa begränsningar kan du justera resurs typens hållbarhets nivå:

* Nodtyper med hållbarhets nivåer för silver eller guld kan inte nedgraderas till brons.
* Det kan ta några timmar att uppgradera från brons till silver eller guld.
* När du ändrar hållbarhets nivå måste du uppdatera den i både Service Fabric tilläggs konfiguration i den virtuella datorns skalnings uppsättnings resurs och i definitionen av nodtypen i Service Fabric kluster resursen. Dessa värden måste vara identiska.

Ett annat övervägande när kapacitets planering är Tillförlitlighets nivån för klustret, vilket avgör stabiliteten i system tjänster och det övergripande klustret, enligt beskrivningen i nästa avsnitt.

## <a name="reliability-characteristics-of-the-cluster"></a>Tillförlitlighets egenskaper för klustret

Kluster *Tillförlitlighets nivån* avgör antalet system Services-replikeringar som körs på den primära nodtypen i klustret. Fler repliker, desto mer pålitliga är system tjänsterna (och därför hela klustret).

> [!IMPORTANT]
> Tillförlitlighets nivån ställs in på kluster nivå och bestämmer det lägsta antalet noder av den primära nodtypen. Produktions arbets belastningar kräver en Tillförlitlighets nivå på Silver (större eller lika med fem noder) eller högre.  

Tillförlitlighets nivån kan ha följande värden:

* **Platina** -system tjänster kör med mål replik uppsättnings antal nio
* **Gold** -system Services kör med mål replik uppsättnings antal sju
* **Silver** system tjänster kör med mål replik uppsättnings antal fem
* **Brons** -system tjänster kör med mål replik uppsättnings antal tre

Här är en rekommendation om att välja Tillförlitlighets nivå. Antalet startnoder anges också till det minsta antalet noder för en Tillförlitlighets nivå.


| **Antal noder** | **Tillförlitlighets nivå** |
| --- | --- |
| 1 | *Ange inte `reliabilityLevel` parametern: systemet beräknar det.* |
| 3 | Brons |
| 5 eller 6| Silver |
| 7 eller 8 | Guld |
| 9 och uppåt | Platina |

När du ökar eller minskar storleken på klustret (summan av virtuella dator instanser i alla nodtyper) bör du överväga att uppdatera tillförlitligheten för klustret från en nivå till en annan. Detta utlöser de kluster uppgraderingar som krävs för att ändra antalet replik uppsättningar för system tjänster. Vänta tills den pågående uppgraderingen har slutförts innan du gör några andra ändringar i klustret, t. ex. att lägga till noder.  Du kan övervaka förloppet för uppgraderingen på Service Fabric Explorer eller genom att köra [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)

### <a name="capacity-planning-for-reliability"></a>Kapacitets planering för tillförlitlighet

Klustrets kapacitets krav bestäms av dina särskilda krav för arbets belastning och pålitlighet. Det här avsnittet innehåller allmänna rikt linjer som hjälper dig att komma igång med kapacitets planering.

#### <a name="virtual-machine-sizing"></a>Storlek på virtuell dator

**För produktions arbets belastningar är rekommenderad VM-storlek (SKU) [Standard D2_V2](../virtual-machines/dv2-dsv2-series.md) (eller motsvarande) med minst 50 GB lokal SSD, 2 kärnor och 4 GiB minne.** Minst 50 GB lokal SSD rekommenderas, men vissa arbets belastningar (till exempel sådana som kör Windows-behållare) kräver större diskar. Tänk på följande begränsningar när du väljer andra [storlekar för virtuella datorer](../virtual-machines/sizes-general.md) för produktions arbets belastningar:

- Partiella kärn VM-storlekar som standard a0 stöds inte.
- *A-serien* VM-storlekar stöds inte av prestanda skäl.
- Virtuella datorer med låg prioritet stöds inte.

#### <a name="primary-node-type"></a>Typ av primär nod

**Produktions arbets belastningar** i Azure kräver minst fem primära noder (VM-instanser) och Tillförlitlighets nivån Silver. Vi rekommenderar att du dedikerar klustrets primära nodtyp till system tjänster och använder placerings begränsningar för att distribuera programmet till sekundära nodtyper.

**Test arbets belastningar** i Azure kan köra minst en eller tre primära noder. Om du vill konfigurera ett kluster för en nod måste du se till att `reliabilityLevel` inställningen är helt utelämnad i Resource Manager-mallen (genom att ange ett tomt sträng värde för `reliabilityLevel` är inte tillräckligt). Om du ställer in ett kluster för en nod med Azure Portal görs den här konfigurationen automatiskt.

> [!WARNING]
> Kluster med en nod körs med en särskild konfiguration utan pålitlighet och där skala ut inte stöds.

#### <a name="non-primary-node-types"></a>Typer som inte är primära noder

Det minsta antalet noder för en icke-primär nodtyp beror på den speciella [hållbarhets nivån](#durability-characteristics-of-the-cluster) för nodtypen. Du bör planera antalet noder (och hållbarhets nivå) baserat på antalet repliker av program eller tjänster som du vill köra för nodtypen och beroende på om arbets belastningen är tillstånds känslig eller tillstånds lös. Tänk på att du kan öka eller minska antalet virtuella datorer i en Node-typ när du har distribuerat klustret.

##### <a name="stateful-workloads"></a>Tillstånds känsliga arbets belastningar

För tillstånds känsliga produktions arbets belastningar med Service Fabric [pålitliga samlingar eller pålitliga aktörer](service-fabric-choose-framework.md)rekommenderas minst fem mål repliker. Med detta, i stabilt tillstånd, slutar du med en replik (från en replik uppsättning) i varje fel domän och uppgraderings domän. I allmänhet använder du Tillförlitlighets nivån som du anger för system tjänster som en guide för antalet repliker som du använder för dina tillstånds känsliga tjänster.

##### <a name="stateless-workloads"></a>Tillstånds lösa arbets belastningar

För tillstånds lösa arbets belastningar för produktion är den lägsta tillåtna storleken som stöds av en icke-primär nodtyp tre för att upprätthålla kvorum, men en nodtyp på fem rekommenderas.

## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar klustret bör du gå igenom `Not Allowed` [kluster uppgraderings principerna](service-fabric-cluster-fabric-settings.md) för att undvika att behöva återskapa klustret senare på grund av att system konfigurations inställningarna i övrigt inte kan ändras.

Mer information om kluster planering finns i:

* [Beräkningsplanering och skalning](service-fabric-best-practices-capacity-scaling.md)
* [Kapacitets planering för Service Fabric program](service-fabric-capacity-planning.md)
* [Planering för haveri beredskap](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
