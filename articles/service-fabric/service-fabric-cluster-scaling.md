---
title: Azure Service Fabric kluster skalning
description: Lär dig mer om att skala Azure Service Fabric-kluster i eller ut och upp eller ned. När program kräver ändringar kan du Service Fabric kluster.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591799"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skala Azure Service Fabric-kluster
Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en nod. Kluster kan innehålla potentiellt tusentals noder. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurserna för noderna).  Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.  När klustret skalas, skalas programmen automatiskt.

Varför ska du skala klustret? Program krav ändras med tiden.  Du kan behöva öka kluster resurserna för att möta ökad program belastning eller nätverks trafik eller minska kluster resurserna när efter frågan går vidare.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skala in och ut eller vågrät skalning
Ändrar antalet noder i klustret.  När de nya noderna ansluter till klustret, flyttar [kluster resurs hanteraren](service-fabric-cluster-resource-manager-introduction.md) tjänster till dem som minskar belastningen på de befintliga noderna.  Du kan också minska antalet noder om klustrets resurser inte används effektivt.  När noder lämnar klustret flyttas tjänsterna från dessa noder och belastningen ökar på de återstående noderna.  Att minska antalet noder i ett kluster som körs i Azure kan spara pengar, eftersom du betalar för antalet virtuella datorer som du använder och inte arbets belastningen på de virtuella datorerna.  

- Fördelar: oändlig skala i teorin.  Om ditt program har utformats för skalbarhet kan du aktivera obegränsad tillväxt genom att lägga till fler noder.  Verktyget i moln miljöer gör det enkelt att lägga till eller ta bort noder, så det är enkelt att justera kapaciteten och du betalar bara för de resurser du använder.  
- Nack delar: program måste [utformas för skalbarhet](service-fabric-concepts-scalability.md).  Program databaser och persistence kan kräva ytterligare arkitektur arbete för att skala.  [Pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md) i Service Fabric tillstånds känsliga tjänster, men gör det mycket enklare att skala dina program data.

Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan skalas in eller ut oberoende av varandra, ha olika port uppsättningar öppna och kan ha olika kapacitets mått. 

När du skalar ett Azure-kluster bör du ha följande rikt linjer i åtanke:
- primära nodtyper som kör produktions arbets belastningar bör alltid ha fem eller fler noder.
- icke-primära nodtyper som kör tillstånds känsliga produktions arbets belastningar bör alltid ha fem eller fler noder.
- icke-primära nodtyper som kör tillstånds lösa produktions arbets belastningar bör alltid ha två eller flera noder.
- Alla nodtyper för [hållbarhets nivån](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) guld eller silver bör alltid ha fem eller fler noder.
- Ta inte bort slumpmässiga virtuella dator instanser/noder från en nodtyp, Använd alltid skalnings uppsättningen för virtuella datorer i funktionen. Borttagning av slumpmässiga VM-instanser kan negativt påverka systemets möjlighet att belastningsutjämna.
- Om du använder regler för automatisk skalning ställer du in reglerna så att skalning i (tar bort VM-instanser) görs en nod i taget. Att skala ned fler än en instans i taget är inte säkert.

Eftersom Service Fabric-nodtypen i klustret består av virtuella datorers skalnings uppsättningar på Server delen, kan du [Konfigurera regler för automatisk skalning eller manuellt skala](service-fabric-cluster-scale-in-out.md) varje nodtyp/virtuell dators skalnings uppsättning.

### <a name="programmatic-scaling"></a>Programmerings skalning
I många fall kan du [skala upp ett kluster manuellt eller med regler för automatisk skalning](service-fabric-cluster-scale-in-out.md) som är effektiva lösningar. För mer avancerade scenarier kan de inte vara rätt anpassade. Eventuella nack delar med dessa metoder är:

- Manuell skalning kräver att du loggar in och uttryckligen begär skalnings åtgärder. Om skalnings åtgärder krävs ofta eller på oväntade tider är det inte säkert att den här metoden är en felfri lösning.
- När AutoScale-regler tar bort en instans från en skalnings uppsättning för virtuella datorer tar de inte automatiskt bort kunskapen från den noden från det associerade Service Fabric-klustret om nodtypen har en hållbarhets nivå på silver eller guld. Eftersom AutoScale-regler fungerar på skalnings uppsättnings nivå (i stället för på Service Fabric nivå), kan regler för automatisk skalning ta bort Service Fabric noder utan att stänga av dem på ett smidigt sätt. Den här oartig tar bort "Ghost" Service Fabric nodens tillstånd bakom efter skalnings åtgärder. En enskild person (eller tjänst) måste regelbundet rensa bort status för noden i Service Fabric klustret.
- En nodtyp med en hållbarhets nivå på guld eller silver rensar automatiskt bort noder, så ingen ytterligare rensning krävs.
- Även om det finns [många mått](../azure-monitor/autoscale/autoscale-common-metrics.md) som stöds av regler för automatisk skalning, är det fortfarande en begränsad mängd. Om scenariot kräver skalning baserat på ett mått som inte täcks i den uppsättningen, kan det hända att regler för automatisk skalning inte är ett lämpligt alternativ.

Hur du bör använda Service Fabric skalning beror på ditt scenario. Om skalning är ovanligt räcker det förmodligen att lägga till eller ta bort noder manuellt. För mer komplexa scenarier visar autoskalning av regler och SDK: er möjlighet att skala programmatiskt erbjuda kraftfulla alternativ.

Det finns Azure-API: er som gör det möjligt för program att program mässigt fungera med skalnings uppsättningar för virtuella datorer och Service Fabric kluster. Om befintliga alternativ för automatisk skalning inte fungerar för ditt scenario gör dessa API: er det möjligt att implementera anpassad skalnings logik. 

En metod för att implementera den här funktionen för automatisk skalning är att lägga till en ny tillstånds lös tjänst i Service Fabric program för att hantera skalnings åtgärder. Genom att skapa en egen skalnings tjänst får du den högsta graden av kontroll och anpassningsbarhet över programmets skalnings beteende. Detta kan vara användbart för scenarier som kräver exakt kontroll över när eller hur ett program skalas in eller ut. Den här kontrollen kommer dock att ha en kompromiss mellan kod komplexitet. Med den här metoden innebär det att du behöver egen skalnings kod som inte är trivial. I tjänstens `RunAsync` metod kan en uppsättning utlösare avgöra om skalning krävs (inklusive kontrol lera parametrar som maximal kluster storlek och skalning cooldowns).   

Det API som används för interaktions uppsättning för virtuell dator (båda för att kontrol lera det aktuella antalet virtuella dator instanser och för att ändra det) är ett [hanterings bibliotek för hanterings biblioteket i Fluent Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). I Fluent Compute-biblioteket finns ett lättanvänt API för att interagera med skalnings uppsättningar för virtuella datorer.  Om du vill interagera med själva Service Fabric själva klustret använder du [system. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient).

Skalnings koden behöver inte köras som en tjänst i klustret för att skalas, men. Både `IAzure` och `FabricClient` kan ansluta till sina associerade Azure-resurser via en fjärr anslutning, så att skalnings tjänsten enkelt kan vara ett konsol program eller en Windows-tjänst som körs utanför Service Fabric-programmet.

Utifrån dessa begränsningar kanske du vill [implementera mer anpassade modeller för automatisk skalning](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skala upp och ned eller lodrät skalning 
Ändrar resurserna (CPU, minne eller lagring) för noder i klustret.
- Fördelar: program vara och program arkitektur är oförändrade.
- Nack delar: begränsad skala, eftersom det finns en gräns för hur mycket du kan öka resurser på enskilda noder. Nedtid, eftersom du måste göra fysiska eller virtuella datorer offline för att kunna lägga till eller ta bort resurser.

Skalnings uppsättningar för virtuella datorer är en Azure Compute-resurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Azure-kluster har [kon figurer ATS som en separat skalnings uppsättning](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat.  Att skala upp eller ned en nodtyp omfattar att lägga till en ny nodtyp (med uppdaterad VM-SKU) och ta bort den gamla nodtypen.

När du skalar ett Azure-kluster bör du ha följande rikt linjer i åtanke:
- Om du skalar ned en typ av primär nod bör du aldrig skala ut mer än vad [Tillförlitlighets nivån](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) tillåter.

Processen att skala upp eller ned en nodtyp är olika beroende på om det är en icke-primär eller primär nodtyp.

### <a name="scaling-non-primary-node-types"></a>Skala icke-primära nodtyper
Skapa en ny nodtyp med de resurser du behöver.  Uppdatera placerings begränsningarna för att köra tjänster för att inkludera den nya nodtypen.  Gradvis (en i taget), minska antalet instanser för de gamla instans antalet för nodtypen till noll så att klustrets tillförlitlighet inte påverkas.  Tjänsterna migreras gradvis till den nya nodtypen eftersom den gamla nodtypen tas ur bruk.

### <a name="scaling-the-primary-node-type"></a>Skala den primära nodtypen
Distribuera en ny primär nodtyp med uppdaterad VM-SKU och inaktivera sedan de ursprungliga primära nodtypen en i taget, så att system tjänsterna migreras till den nya skalnings uppsättningen. Kontrol lera att klustret och de nya noderna är felfria och ta sedan bort den ursprungliga skalnings uppsättningen och nodens tillstånd för de borttagna noderna.

Om detta inte är möjligt kan du skapa ett nytt kluster och [återställa program tillstånd](service-fabric-reliable-services-backup-restore.md) (om det är tillämpligt) från det gamla klustret. Du behöver inte återställa något system tjänst tillstånd, de återskapas när du distribuerar dina program till det nya klustret. Om du precis har kört tillstånds lösa program i klustret, så kan du distribuera dina program till det nya klustret, men du har inget att återställa.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [program skalbarhet](service-fabric-concepts-scalability.md).
* [Skala ett Azure-kluster in eller ut](service-fabric-tutorial-scale-cluster.md).
* [Skala ett Azure-kluster program mässigt](service-fabric-cluster-programmatic-scaling.md) med hjälp av Fluent Azure Compute SDK.
* [Skala ett fristående kluster in eller ut](service-fabric-cluster-windows-server-add-remove-nodes.md).

