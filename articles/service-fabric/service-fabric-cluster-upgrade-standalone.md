---
title: Uppgradera ett fristående Azure Service Fabric-kluster
description: Lär dig mer om att uppgradera versionen eller konfigurationen av ett fristående Azure Service Fabric-kluster.  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 519a9163d16fda2dd9fcf49cf22fe4ad4a272e09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260985"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Uppgradera och uppdatera ett Service Fabric fristående kluster

För alla moderna system är det viktigt att utforma för att kunna uppnå långsiktig framgång för din produkt. Ett fristående Azure Service Fabric fristående kluster är en resurs som du äger. I den här artikeln beskrivs vad som kan uppgraderas eller uppdateras.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Styra den infrastruktur resurs version som körs i klustret
Kontrol lera att klustret alltid kör en [Service Fabric-version som stöds](service-fabric-versions.md). När Microsoft tillkännager lanseringen av en ny version av Service Fabric, markeras den tidigare versionen för slut för ande av support efter minst 60 dagar från dagen för meddelandet. Nya versioner presenteras [i Service Fabric teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Den nya versionen är tillgänglig och kan väljas nu.

Du kan ange att klustret ska ta emot automatiska Fabric-uppgraderingar när de lanseras av Microsoft, eller så kan du manuellt välja en infrastruktur version som stöds och som du vill att klustret ska vara på. Mer information finns [i uppgradera den Service Fabric version som körs i klustret](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Anpassa konfigurations inställningar

Många olika [konfigurations inställningar](service-fabric-cluster-manifest.md) kan anges i *ClusterConfig.jspå* filen, till exempel Tillförlitlighets nivån för klustret och nodens egenskaper.  Läs mer om hur du [uppgraderar konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).  Många andra, mer avancerade inställningar kan också anpassas.  Mer information finns i [Service Fabric Cluster Fabric-inställningar](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definiera egenskaper för nod
Ibland kanske du vill se till att vissa arbets belastningar bara körs på vissa typer av noder i klustret. En viss arbets belastning kan till exempel kräva GPU: er eller SSD medan andra inte är det. För varje nodtyp i ett kluster kan du lägga till anpassade Node-egenskaper till klusternoder. Placerings begränsningar är de instruktioner som är kopplade till enskilda tjänster som väljs för en eller flera Node-egenskaper. Placerings begränsningar definierar var tjänsterna ska köras.

Mer information om hur du använder placerings begränsningar, Node-egenskaper och hur du definierar dem finns i [Egenskaper för noden och placerings begränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Lägg till kapacitets mått
För var och en av nodtypen kan du lägga till anpassade kapacitets mått som du vill använda i dina program för att rapportera belastningen. Mer information om hur du använder kapacitets mått för att rapportera inläsning finns i Service Fabric Cluster Resource Manager-dokument för att [beskriva ditt kluster och dina](service-fabric-cluster-resource-manager-cluster-description.md) [mått och belastning](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Korrigera operativ systemet på klusternoderna
POA (patch Orchestration Application) är ett Service Fabric program som automatiserar operativ Systems korrigeringar på ett Service Fabric kluster utan drift avbrott. [Programmet för uppdaterings dirigering för Windows](service-fabric-patch-orchestration-application.md) kan distribueras i klustret för att installera uppdateringar på ett dirigerat sätt och samtidigt hålla tjänsterna tillgängliga hela tiden. 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du anpassar några av [inställningarna för Service Fabric-klustrets infrastruktur resurser](service-fabric-cluster-fabric-settings.md)
* Lär dig hur du [skalar upp och ut ditt kluster](service-fabric-cluster-scale-in-out.md)
* Lär dig mer om [program uppgraderingar](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
