---
title: Så här konfigurerar du JMX-mått – Azure Monitor Application Insights för Java
description: Konfigurera ytterligare JMX-mått samling för Azure Monitor Application Insights Java agent
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610020"
---
# <a name="configuring-jmx-metrics"></a>Konfigurera JMX-mått

Application Insights Java 3,0-agenten samlar in några av JMX-måtten som standard, men i många fall är detta inte tillräckligt. Det här dokumentet beskriver konfigurations alternativet JMX i information.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Hur gör jag för att samla in ytterligare JMX-mått?

JMX Metrics-insamling kan konfigureras genom att lägga till ett ```"jmxMetrics"``` avsnitt i applicationinsights.jsi filen. Du kan ange namnet på måttet som du vill att det ska visas i Azure Portal i Application Insights-resursen. Du måste definiera objekt namn och-attribut för varje mått som du vill samla in.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Hur gör jag för att vet du vilka mått som är tillgängliga för konfiguration?

Du Nailed det – du måste känna till objekt namn och attribut, dessa egenskaper är olika för olika bibliotek, ramverk och program servrar och är ofta inte väl dokumenterade. Om du vill hämta objekt namn och attribut måste du Visa MBean-trädet. En MBean är ett hanterat Java-objekt som kan representera en enhet, ett program eller en resurs och har en uppsättning attribut. 

Om du vill visa tillgängliga mått och bläddra igenom tillgängliga mått, rekommenderar vi att du använder [Java-Mission](https://www.oracle.com/java/technologies/jdk-mission-control.html).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Hur navigerar du i Java-uppdraget för att komma till rätt mått?

När du kör verktyget för Java-Mission har du ett urval av JVMs som är tillgängliga på den vänstra sidan. Klicka på den aktuella processen under fliken "JVM Browser". Vänta tills JMC läser in instrument panelen för processen och välj fliken "MBean Browser" längst ned (se nedan). JMC måste finnas i samma mapp som JVM och din process/app måste vara igång.

![Skärm bild av JMC MBean-webbläsare](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Hur kommer du till de mått som jag vill ha och de nödvändiga attributen?

MBean-webbläsaren öppnar MBean-trädet med listan över kategorier som kan expanderas. Om du väljer en kategori till vänster öppnas listan med attribut till höger. Nedan visas ett exempel på ett mått, dess objekt namn och attribut. Attributen kan vara kapslade, som i exemplet nedan.

![Skärm bild av JMC MBean-träd](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Konfigurations exempel

Från valet som du ser i bilden ovan kan du konfigurera några mått. Det första är ett exempel på ett kapslat mått – `LastGcInfo` som har flera egenskaper och vi vill samla in `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Typer av insamlade mått och tillgängliga konfigurations alternativ?

Vi stöder numeriska och booleska JMX-mått, medan andra typer inte stöds och kommer att ignoreras. 

Jokertecken och aggregerade attribut stöds för närvarande inte, det är anledningen till att varje attribut objekt namn/attribut-paret måste konfigureras separat. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Var hittar jag JMX-måtten i Application Insights?

När ditt program körs och JMX-måtten samlas in kan du visa dem genom att gå till Azure Portal och navigera till Application Insights-resursen. Under fliken mått väljer du den nedrullningsbara List rutan som visas nedan för att visa måtten.

![Skärm bild av mått i portalen](media/java-ipa/jmx/jmx-portal.png)
