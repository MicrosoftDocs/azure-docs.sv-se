---
title: Balansera ditt Azure Service Fabric-kluster
description: En introduktion till att balansera klustret med Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146202"
---
# <a name="balancing-your-service-fabric-cluster"></a>Balansera Service Fabric-klustret
Service Fabric Cluster Resource Manager stöder dynamiska inläsnings ändringar, som reagerar på tillägg eller borttagning av noder eller tjänster. Det korrigerar också automatiskt begränsnings överträdelser och proaktivt balanserar klustret igen. Men hur ofta är dessa åtgärder vidtagna och vad utlöser dem?

Det finns tre olika arbets kategorier som kluster resurs hanteraren utför. De är:

1. Placering – det här steget handlar om att placera alla tillstånds känsliga repliker eller tillstånds lösa instanser som saknas. Placering inkluderar både nya tjänster och hantering av tillstånds känsliga repliker eller tillstånds lösa instanser som har misslyckats. Att ta bort och släppa repliker eller instanser hanteras här.
2. Begränsnings kontroller – det här steget söker efter och korrigerar överträdelser av de olika placerings begränsningarna (regler) i systemet. Exempel på regler är saker som att se till att noderna inte överbelastas och att tjänstens placerings begränsningar är uppfyllda.
3. Balansering – det här steget kontrollerar om ombalansering krävs baserat på den konfigurerade önskade nivån av saldo för olika mått. I så fall försöker den hitta en ordning i klustret som är mer balanserade.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurera timers för kluster resurs hanterare
Den första uppsättningen kontroller kring balansering är en uppsättning timers. Dessa timers styr hur ofta kluster resurs hanteraren undersöker klustret och vidtar lämpliga åtgärder.

Var och en av de olika typerna av korrigeringar som kluster resurs hanteraren kan göra styrs av en annan timer som styr dess frekvens. När varje timer utlöses schemaläggs aktiviteten. Som standard är Resource Manager:

* kontrollerar sitt tillstånd och tillämpar uppdateringar (som att spela in att en nod är nere) var 1/tionde en sekund
* anger placerings kontroll flaggan varje sekund
* anger flaggan för begränsnings kontroll varje sekund
* anger balanserings flaggan var femte sekund

Exempel på konfigurationen som styr dessa timers finns nedan:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

I dag är kluster resurs hanteraren bara en av de här åtgärderna i taget, i tur och ordning. Detta är anledningen till att vi refererar till dessa timers som "lägsta intervall" och de åtgärder som vidtas när timers går ut som "inställnings flaggor". Kluster resurs hanteraren tar till exempel hand om väntande begär Anden om att skapa tjänster innan du balanserar klustret. Som du kan se när du har angett standard tidsintervall, så söker kluster resurs hanteraren efter allt som krävs för att göra det ofta. Det innebär vanligt vis att de ändringar som har gjorts under varje steg är små. Genom att göra små ändringar ofta kan kluster resurs hanteraren svara när saker händer i klustret. Standard timers tillhandahåller vissa batchar eftersom många av samma typer av händelser ofta inträffar samtidigt. 

Om noderna till exempel inte kan utföras kan de göra hela fel domäner i taget. Alla dessa avbrott samlas in under nästa tillstånds uppdatering efter *PLBRefreshGap*. Korrigeringarna bestäms vid följande placering, begränsnings kontroll och balanserings körningar. Som standard genomsöker inte kluster resurs hanteraren igenom antalet ändringar i klustret och försöker adressera alla ändringar samtidigt. Detta skulle leda till burst-överföring av omsättningen.

Kluster resurs hanteraren behöver också ytterligare information för att avgöra om klustret är förbalanseradt. För att vi har två andra delar av konfigurationen: *BalancingThresholds* och *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Tröskelvärden för utjämning
Ett tröskelvärde för utjämning är huvud kontrollen för att utlösa ombalansering. Tröskelvärdet för Utjämning av mått är ett _förhållande_. Om belastningen för ett mått på den mest inlästa noden dividerat med mängden belastning på den minst inlästa noden överstiger den måttets *BalancingThreshold*, så är klustret obalanserat. En resultat utjämning utlöses nästa gången som kluster resurs hanteraren kontrollerar. *MinLoadBalancingInterval* timer definierar hur ofta kluster resurs hanteraren ska kontrol lera om ombalansering krävs. Kontrollen innebär inte att något händer. 

Tröskelvärden för utjämning definieras per mått som en del av kluster definitionen. Mer information om mått finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Exempel på Utjämnings tröskel][Image1]
</center>

I det här exemplet använder varje tjänst en enhet av ett mått. I det översta exemplet är den maximala belastningen på en nod fem och minimivärdet är två. Anta att tröskelvärdet för Utjämning av mått är tre. Eftersom förhållandet i klustret är 5/2 = 2,5 och det är mindre än det angivna tröskelvärdet för Utjämning av tre, är klustret balanserade. Ingen balansering utlöses när kluster resurs hanteraren kontrollerar.

I det nedre exemplet är den maximala belastningen på en nod 10, medan minimivärdet är två, vilket resulterar i förhållandet fem. Fem är större än det angivna tröskelvärdet för Utjämning av tre för det måttet. Som ett resultat av detta kommer en ombalansering att schemaläggas nästa gången den balanserande timern utlöses. I en situation som denna belastning distribueras vanligt vis till Nod3. Eftersom Service Fabric Cluster Resource Manager inte använder en girig metod kan en del belastning också distribueras till NOD2. 

<center>

![Exempel åtgärder för Utjämnings tröskel][Image2]
</center>

> [!NOTE]
> "Balansering" hanterar två olika strategier för att hantera belastning i klustret. Standard strategin som kluster resurs hanteraren använder är att distribuera belastningen mellan noderna i klustret. Den andra strategin är [defragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentering utförs under samma balanserings körning. Strategier för balansering och defragmentering kan användas för olika mått i samma kluster. En tjänst kan ha både balans-och defragmentering av mått. För defragmentering-mått utlöser förhållandet mellan belastningarna i klustret ombalansering när den är _lägre än_ tröskelvärdet för utjämning. 
>

Att komma under tröskelvärdet för utjämning är inte ett explicit mål. Tröskelvärden för utjämning är bara en *utlösare*. När balanseringen körs avgör kluster resurs hanteraren vilka förbättringar det kan göra, om det finns några. Bara på grund av att en balanserings sökning har inaktiverats betyder det inte att något rör sig. Ibland bal anse ras klustret men är för begränsat till rätt. Förbättringarna kräver även förflyttningar som är för [dyra](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="activity-thresholds"></a>Aktivitets trösklar
Ibland, även om noderna är relativt obalanserade, är den *totala* belastningen i klustret låg. Brist på inläsning kan vara en tillfällig DIP eller eftersom klustret är nytt och bara får startat. I båda fallen kanske du inte vill spendera tid på klustret eftersom det inte finns något att vinna. Om klustret har genomgått balansering kan du spendera nätverks-och beräknings resurser för att flytta saker runt om, utan att göra stora *absoluta* skillnader. För att undvika onödiga flyttningar finns det en annan kontroll som kallas för aktivitets trösklar. Med aktivitets trösklar kan du ange en viss absolut lägre gräns för aktiviteten. Om ingen nod är över det här tröskelvärdet utlöses inte balanseringen även om tröskelvärdet för utjämning är uppfyllt.

Anta att vi behåller vårt tröskelvärde för Utjämning av tre för detta mått. Vi antar också att vi har ett aktivitets tröskelvärde på 1536. I det första fallet, medan klustret är obalanserat enligt tröskelvärdet för utjämning, finns det ingen nod som uppfyller tröskelvärdet för aktiviteten, så ingenting händer. I det nedre exemplet är Nod1 över aktivitets tröskelvärdet. Eftersom både tröskelvärdet för utjämning och aktivitets tröskeln för måttet överskrids, schemaläggs balanseringen. Vi kan till exempel titta på följande diagram: 

<center>

![Exempel på aktivitets tröskel][Image3]
</center>

Precis som tröskelvärden för utjämning definieras aktivitetens tröskelvärden per mått via kluster definitionen:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Tröskelvärden för utjämning och aktivitet är båda knutna till en speciell mått utjämning utlöses endast om både tröskelvärdet för Utjämnings tröskel och aktivitet har överskridits för samma mått.

> [!NOTE]
> Om inget värde anges är tröskelvärdet för Utjämning av mått 1 och aktivitetens tröskelvärde 0. Det innebär att kluster resurs hanteraren kommer att försöka hålla det måttet perfekt balanserade för en bestämd belastning. Om du använder anpassade mått rekommenderar vi att du uttryckligen definierar egna tröskelvärden för balans och aktivitet för dina mått. 
>

## <a name="balancing-services-together"></a>Balansera tjänster tillsammans
Huruvida klustret är obalanserat eller inte är ett företagsomfattande beslut. Det sätt vi tar på att åtgärda det är dock att flytta enskilda tjänst repliker och instanser. Detta är meningsfullt, rätt? Om minnet är staplat på en nod kan flera repliker eller instanser bidra till den. Att åtgärda obalansen kan kräva att alla tillstånds känsliga repliker eller tillstånds lösa instanser flyttas som använder det obalanserade måttet.

Ibland kan en tjänst som inte är överbalanserad flyttas (kom ihåg diskussionen om lokala och globala vikter tidigare). Varför flyttas tjänsten när alla de här tjänst måtten balanserades? Nu ska vi se ett exempel:

- Anta att det finns fyra tjänster, Service1, service2, Service3 och Service4. 
- Service1 rapporterar måtten Metric1 och Metric2. 
- Service2 rapporterar måtten Metric2 och Metric3. 
- Service3 rapporterar måtten Metric3 och Metric4.
- Service4 rapporterar måttet Metric99. 

Surely kan du se var vi ska: det finns en kedja! Vi har egentligen inte fyra oberoende tjänster, vi har tre tjänster som är relaterade till varandra och som är inaktuella.

<center>

![Diagram som visar hur du balanserar tjänster tillsammans.][Image4]
</center>

På grund av den här kedjan är det möjligt att en obalans i mått 1-4 kan orsaka att repliker eller instanser som hör till tjänster 1-3 kan flyttas runt. Vi vet också att en obalans i måtten 1, 2 eller 3 inte kan orsaka rörelser i Service4. Det skulle inte finnas någon punkt eftersom du flyttar de repliker eller instanser som hör till Service4, men det kan vara absolut ingen att påverka saldot för måtten 1-3.

Kluster resurs hanteraren tar automatiskt reda på vilka tjänster som är relaterade. Att lägga till, ta bort eller ändra måtten för tjänster kan påverka deras relationer. Till exempel kan två körningar av balanserings service2 ha uppdaterats för att ta bort Metric2. Detta bryter kedjan mellan Service1 och service2. I stället för två grupper av relaterade tjänster finns det tre:

<center>

![Diagram som visar att kluster resurs hanteraren avgör vilka tjänster som är relaterade.][Image5]
</center>

## <a name="next-steps"></a>Nästa steg
* Mått är hur Service Fabric Cluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
* Rörelse kostnaden är ett sätt att signalera till kluster resurs hanteraren att vissa tjänster är dyrare att flytta än andra. Mer information om rörelse kostnad finns i [den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)
* Kluster resurs hanteraren har flera begränsningar som du kan konfigurera för att sakta ned omsättningen i klustret. De är normalt inte nödvändiga, men om du behöver dem kan du lära dig mer om dem [här](service-fabric-cluster-resource-manager-advanced-throttling.md)
* Kluster resurs hanteraren kan identifiera och hantera under kluster (en situation som ibland uppstår när du använder placerings begränsningar och balansering). Information om hur under kluster kan påverka balanseringen och hur du kan hantera det finns [här](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
