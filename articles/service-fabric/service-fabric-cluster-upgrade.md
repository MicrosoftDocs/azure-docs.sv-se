---
title: Uppgradera Azure Service Fabric-kluster
description: Lär dig mer om alternativ för att uppdatera Azure Service Fabric-klustret
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731125"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Uppgradera och uppdatera Azure Service Fabric-kluster

Ett Azure Service Fabric-kluster är en resurs som du äger, men den hanteras delvis av Microsoft. I den här artikeln beskrivs alternativen för när och hur du uppdaterar Azure Service Fabric-klustret.

## <a name="automatic-versus-manual-upgrades"></a>Automatiska kontra manuella uppgraderingar

Det är viktigt att säkerställa att Service Fabric klustret alltid kör en [runtime-version som stöds](service-fabric-versions.md). Varje gången som Microsoft tillkännager lanseringen av en ny version av Service Fabric, har den tidigare versionen marker ATS för *slut på support* efter minst 60 dagar från det datumet. Nya versioner presenteras i [Service Fabric teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

Fjorton dagar innan utgångs tiden för klustret körs, genereras en hälso händelse som placerar klustret i ett *varnings* hälso tillstånd. Klustret är i ett varnings tillstånd tills du uppgraderar till en runtime-version som stöds.

Du kan ange att klustret ska ta emot automatiska Service Fabric uppgraderingar när de släpps av Microsoft, eller så kan du manuellt välja från en lista över versioner som stöds för tillfället. De här alternativen finns i avsnittet **infrastruktur uppgraderingar** i Service Fabric kluster resursen.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Välj automatiska eller manuella uppgraderingar i avsnittet &quot;Fabric-uppgraderingar&quot; i kluster resursen i Azure Portal.":::

Du kan också ange kluster uppgraderings läge och välja en körnings version [med hjälp av en Resource Manager-mall](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Automatiska uppgraderingar är det rekommenderade uppgraderings läget, eftersom det här alternativet säkerställer att klustret förblir i ett tillstånd som stöds och fördelar med de senaste korrigeringarna och funktionerna, samtidigt som du kan schemalägga uppdateringar på ett sätt som är minst störande för dina arbets belastningar med en [våg distributions](#wave-deployment-for-automatic-upgrades) strategi.

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave-distribution för automatiska uppgraderingar

Med Wave-distribution kan du minimera avbrott i en uppgradering till klustret genom att välja förfallo nivå för en uppgradering, beroende på din arbets belastning. Du kan till exempel ställa in en   ->    ->  pipeline för *produktion av produktions* våg i test fasen för dina olika Service Fabric-kluster för att testa kompatibiliteten för en körnings uppgradering innan du använder den i produktions arbets belastningarna.

Om du vill välja Wave-distribution anger du något av följande Wave-värden för klustret (i distributions mal len):

* **Wave 0**: kluster uppdateras så fort en ny Service Fabric version lanseras. Avsett för test-/dev-kluster.
* **Wave 1**: kluster uppdateras en vecka (sju dagar) efter att en ny version har släppts. Avsett för för produktions-och mellanlagrings kluster.
* **Wave 2**: kluster uppdateras två veckor (14 dagar) efter att en ny version har släppts. Avsedd för produktions kluster.

Du kan registrera dig för e-postaviseringar med länkar för att få hjälp om uppgraderingen av klustret Miss lyckas. Se [Wave-distribution för automatiska uppgraderingar](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) för att komma igång.

## <a name="phases-of-automatic-upgrade"></a>Faser av automatisk uppgradering

Microsoft underhåller den Service Fabric körnings kod och konfiguration som körs i ett Azure-kluster. Vi utför automatiskt övervakade uppgraderingar till program varan vid behov. Dessa uppgraderingar kan vara kod, konfiguration eller både och. För att minimera effekten av dessa uppgraderingar i dina program utförs de i följande faser:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fas 1: en uppgradering utförs med alla kluster hälso principer

Under den här fasen fortsätter uppgraderingen en uppgraderings domän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Kluster hälso principerna (för nod hälsa och program hälsa) följer under uppgraderingen.

Om klustrets hälso principer inte uppfylls återställs uppgraderingen och ett e-postmeddelande skickas till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Ett meddelande om att en kluster uppgradering skulle återställas.
* Föreslagna åtgärds åtgärder, om sådana finns.
* Antalet dagar (*n*) tills vi kör fas 2.

Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. Efter de *n* dagar från det datum då e-postmeddelandet skickades, fortsätter vi till fas 2.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Den här situationen kan inträffa under den inledande uppgraderingen eller någon av uppgraderingarna i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning för att undvika att skicka överflödiga e-postmeddelanden. Att ta emot ett e-postmeddelande indikerar ett undantag till normala åtgärder. Vi förväntar oss att de flesta av kluster uppgraderingarna lyckas utan att påverka din program tillgänglighet.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fas 2: en uppgradering utförs endast med standard hälso principer

Hälso principerna i den här fasen anges på ett sådant sätt att antalet program som är felfria i början av uppgraderingen förblir desamma under uppgraderings processen. Som i fas 1 fortsätter fas 2-uppgraderingar en uppgraderings domän i taget och de program som kördes i klustret fortsätter att köras utan avbrott. Kluster hälso principerna (en kombination av nods hälsa och hälso tillståndet alla program som körs i klustret) följer under uppgraderingen.

Om klustrets hälso principer i själva verket inte uppfylls, återställs uppgraderingen. Sedan skickas ett e-postmeddelande till Prenumerationens ägare. E-postmeddelandet innehåller följande information:

* Ett meddelande om att en kluster uppgradering skulle återställas.
* Föreslagna åtgärds åtgärder, om sådana finns.
* Antalet dagar (n) tills vi kör fas 3.

Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. En påminnelse via e-post skickas ett par dagar innan n dagar är upp. Efter de n dagar från det datum då e-postmeddelandet skickades, fortsätter vi till fas 3. E-postmeddelandena som vi skickar till dig i fas 2 måste fattas allvarligt och åtgärder måste vidtas.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Detta kan inträffa under den inledande uppgraderingen eller någon av uppgraderings omgången i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fas 3: en uppgradering utförs med hjälp av aggressiva hälso principer

De här hälso principerna i den här fasen är inriktad på att uppgraderingen slutförs i stället för programmens hälsa. Några kluster uppgraderingar slutförs i den här fasen. Om klustret kommer till den här fasen, är det en chans att ditt program blir ohälsosamt och/eller förlorar tillgänglighet.

På samma sätt som i de andra två faserna uppgraderar fas 3 en uppgraderings domän i taget.

Om klustrets hälso principer inte uppfylls, återställs uppgraderingen. Vi försöker utföra samma uppgradering några gånger om eventuella uppgraderingar inte kunde utföras av infrastruktur skäl. Därefter fästs klustret så att det inte längre får support och/eller uppgraderingar.

Ett e-postmeddelande med den här informationen skickas till Prenumerationens ägare, tillsammans med åtgärder för att åtgärda problemet. Vi förväntar sig inte några kluster att komma in i ett tillstånd där fas 3 har misslyckats.

Om klustrets hälso principer är uppfyllda betraktas uppgraderingen som lyckad och har marker ATS som slutförd. Detta kan inträffa under den inledande uppgraderingen eller någon av uppgraderings omgången i den här fasen. Det finns ingen e-postbekräftelse för lyckad körning.

## <a name="custom-policies-for-manual-upgrades"></a>Anpassade principer för manuella uppgraderingar

Du kan ange anpassade principer för manuella kluster uppgraderingar. Dessa principer används varje gång du väljer en ny körnings version, som utlöser systemet för att starta uppgraderingen av klustret. Om du inte åsidosätter principerna används standardvärdena. Mer information finns i [Ange anpassade principer för manuella uppgraderingar](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>Andra kluster uppdateringar

Utanför uppgraderingen av körnings miljön finns det ett antal andra åtgärder som du kan behöva utföra för att hålla klustret uppdaterat, inklusive följande:

### <a name="managing-certificates"></a>Hantera certifikat

Service Fabric använder de [X. 509-Server certifikat](service-fabric-cluster-security.md) som du anger när du skapar ett kluster för att skydda kommunikationen mellan klusternoder och autentisera klienter. Du kan lägga till, uppdatera eller ta bort certifikat för klustret och klienten i [Azure Portal](https://portal.azure.com) eller med PowerShell/Azure CLI.  Läs mer i [Lägg till eller ta bort certifikat](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>Öppna program portar

Du kan ändra program portar genom att ändra Load Balancer resurs egenskaper som är associerade med nodtypen. Du kan använda Azure Portal, eller så kan du använda PowerShell/Azure CLI. Mer information finns i [öppna program portar för ett kluster](create-load-balancer-rule.md).

### <a name="defining-node-properties"></a>Definiera egenskaper för nod

Ibland kanske du vill se till att vissa arbets belastningar bara körs på vissa typer av noder i klustret. En viss arbets belastning kan till exempel kräva GPU: er eller SSD medan andra inte är det. För varje nodtyp i ett kluster kan du lägga till anpassade Node-egenskaper till klusternoder. Placerings begränsningar är de instruktioner som är kopplade till enskilda tjänster som väljs för en eller flera Node-egenskaper. Placerings begränsningar definierar var tjänsterna ska köras.

Mer information om hur du använder placerings begränsningar, Node-egenskaper och hur du definierar dem finns i [Egenskaper för noden och placerings begränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

### <a name="adding-capacity-metrics"></a>Lägga till kapacitets mått

För var och en av nodtypen kan du lägga till anpassade kapacitets mått som du vill använda i dina program för att rapportera belastningen. Mer information om hur du använder kapacitets mått för att rapportera inläsning finns i Service Fabric Cluster Resource Manager-dokument för att [beskriva ditt kluster och dina](service-fabric-cluster-resource-manager-cluster-description.md) [mått och belastning](service-fabric-cluster-resource-manager-metrics.md).

### <a name="customizing-settings-for-your-cluster"></a>Anpassa inställningarna för klustret

Många olika konfigurations inställningar kan anpassas i ett kluster, till exempel Tillförlitlighets nivån för klustret och nodens egenskaper. Mer information finns i [Service Fabric Cluster Fabric-inställningar](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>Uppgradera OS-avbildningar för klusternoder

Att aktivera automatiska uppgradering av operativ system avbildningar för Service Fabric klusternoder är en bra metod. För att göra det finns det flera kluster krav och steg att vidta. Ett annat alternativ är att använda programmet för uppdaterings dirigering (POA, ett Service Fabric program som automatiserar operativ Systems korrigeringar på ett Service Fabric kluster utan drift avbrott. Mer information om de här alternativen finns i [korrigera Windows-operativsystemet i Service Fabric klustret](service-fabric-patch-orchestration-application.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Service Fabric uppgraderingar](service-fabric-cluster-upgrade-version-azure.md)
* Anpassa [Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md)
* [Skala ditt kluster in och ut](service-fabric-cluster-scale-in-out.md)
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md)
