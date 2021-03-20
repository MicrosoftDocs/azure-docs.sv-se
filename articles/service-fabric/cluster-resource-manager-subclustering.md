---
title: Balansering av underklustrade mått
description: Effekterna av placerings begränsningar vid balansering och hur du hanterar dem
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183132"
---
# <a name="balancing-of-subclustered-metrics"></a>Balansering av underklustrade mått

## <a name="what-is-subclustering"></a>Vad är underklustring

Under kluster sker när tjänster med olika placerings begränsningar har ett gemensamt mått och de båda rapport belastningen. Om belastningen som rapporteras av tjänsterna skiljer sig avsevärt, kommer den totala belastningen på noderna att ha en stor standard avvikelse och det ser ut som att klustret är obalanserat, även om det har bästa möjliga balans.

## <a name="how-subclustering-affects-load-balancing"></a>Hur under kluster påverkar belastnings utjämning

Om belastningen som rapporteras av tjänsterna på olika noder skiljer sig avsevärt, kan det se ut som om det finns en stor obalans där det inte finns någon. Om den falska obalansen som orsakas av under klustring är större än den faktiska obalansen, kan det också vara förvirrande att blanda Resource Manager-algoritmen och skapa en optimal balans i klustret.

Anta till exempel att vi har fyra tjänster och att alla rapporterar en belastning för mått Metric1:

* Tjänst A – har en placerings begränsning "NodeType = = klient del", rapporterar en belastning på 10
* Service B – har en placerings begränsning "NodeType = = frontend", rapporterar en belastning på 10
* Service C – har en placerings begränsning "NodeType = = Server del", rapporterar en belastning på 100
* Tjänst D – har en placerings begränsning "NodeType = = Server del", rapporterar en belastning på 100
* Och vi har fyra noder. Två av dem har NodeType inställd som "frontend" och de andra två är "backend"

Och vi har följande placering:

<center>

![Exempel på underklustrad placering][Image1]
</center>

Klustret kan se obalanser, vi har en stor belastning på noderna 3 och 4, men den här placeringen skapar det bästa möjliga saldot i den här situationen.

Resource Manager kan identifiera scenarier med under kluster och i nästan samtliga fall kan det ge optimala balans för den aktuella situationen.

I vissa undantagsfall när Resource Manager inte kan utjämna en optimalt balans för ett underordnat mått kommer det fortfarande att identifiera under klustringen och den genererar en hälso rapport som hjälper dig att åtgärda problemet.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typer av under kluster och hur de hanteras

Under kluster situationer kan klassificeras i tre olika kategorier. Kategorin för en viss del kluster situation bestämmer hur den ska hanteras av Resource Manager.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Första kategori – platt del kluster med åtskilda noder i grupper

Den här kategorin har den enklaste formen av del kluster där noder kan delas upp i olika grupper och varje tjänst kan bara placeras på noder i någon av dessa grupper. Varje nod tillhör bara en grupp och en grupp. Situationen som beskrivs ovan tillhör den här kategorin som gör de flesta av del kluster situationer. 

I den här kategorin kan Resource Manager producera den optimala balansen och ingen ytterligare åtgärd krävs.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Andra kategorin – del klustring med hierarkiska noder i grupper

Den här situationen inträffar när en grupp noder som tillåts för en tjänst är en delmängd av gruppen med noder som tillåts för en annan tjänst. Det vanligaste exemplet på den här situationen är när en viss tjänst har en definierad placerings begränsning och en annan tjänst har ingen placerings begränsning och kan placeras på vilken nod som helst.

Exempel:

* Tjänst A: ingen placerings begränsning
* Tjänst B: placerings begränsning "NodeType = = frontend"
* Service C: placerings begränsning "NodeType = = backend"

Den här konfigurationen skapar en delmängd-supermängd mellan noder för olika tjänster.

<center>

![Del kluster med supermängd][Image2]
</center>

I det här fallet är det en risk att ett underoptimerat saldo blir gjort.

Resurs hanteraren kommer att känna igen den här situationen och skapa en hälso rapport som uppmanar dig att dela upp tjänsten A i två tjänster – service a1 som kan placeras på klient dels-noder och service a2 som kan placeras på backend-noder. Detta kommer tillbaka till den första kategori situationen som kan bal anse ras optimalt.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Tredje kategori – under kluster med partiell överlappning mellan Node-uppsättningar

Den här situationen inträffar när det finns en partiell överlappning mellan uppsättningar av noder som vissa tjänster kan placeras på.

Om vi till exempel har en Node-egenskap med namnet NodeColor och vi har tre noder:

* Nod 1: NodeColor = Red
* Nod 2: NodeColor = blå
* Nod 2: NodeColor = grön

Och vi har två tjänster:

* Tjänst A: med placerings begränsning "Color = = Red | | Färg = = blå "
* Tjänst B: med placerings begränsning "färg = = blå | | Color = = grönt "

Därför kan service A placeras på noderna 1 och 2 och service B kan placeras på noderna 2 och 3.

I det här fallet är det en risk att ett underoptimerat saldo blir gjort.

Resurs hanteraren kommer att känna igen den här situationen och skapa en hälso rapport som uppmanar dig att dela upp några av tjänsterna.

I den här situationen kan inte Resource Manager ge förslag på hur du delar upp tjänsterna, eftersom flera delningar kan utföras och det inte finns något sätt att uppskatta vilket sätt som är det bästa sättet att dela tjänsterna.

## <a name="configuring-subclustering"></a>Konfigurera under kluster

Resurs hanterarens beteende om under klustring kan ändras genom att ändra följande konfigurations parametrar:
* SubclusteringEnabled-parameter bestämmer om resurs hanteraren kommer att ta under kluster i kontot när belastnings utjämning utförs. Om den här parametern är inaktive rad ignorerar Resource Manager underkluster och försöker uppnå optimalt saldo på global nivå. Standardvärdet för den här parametern är false.
* SubclusteringReportingPolicy – anger hur resurs hanteraren ska generera hälso rapporter för hierarkiskt och partiellt överlappande under kluster. Värdet noll innebär att hälso rapporter om under klustring är inaktiverade, "1" innebär att varnings hälso rapporter skapas för under kluster med optimala under kluster och värdet "2" kommer att skapa hälso rapporter för "OK". Standardvärdet för den här parametern är "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg
* Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
* För att ta reda på hur dina tjänster kan begränsas till att endast placeras på vissa noder, se [Node-egenskaper och placerings begränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
