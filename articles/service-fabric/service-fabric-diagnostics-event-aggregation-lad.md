---
title: Händelse agg regering med Linux Azure-diagnostik
description: Lär dig mer om agg regering och insamling av händelser med LAD för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628942"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Händelse agg regering och insamling med Linux Azure-diagnostik
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att logga in på en central plats kan du analysera och felsöka problem i klustret, eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda LAD-tillägget (Linux Azure-diagnostik), som laddar upp loggar till Azure Storage och även har möjlighet att skicka loggar till Azure Application insikter eller Event Hubs. Du kan också använda en extern process för att läsa händelserna från lagringen och placera dem i en analys plattforms produkt, till exempel [Azure Monitor loggar](./service-fabric-diagnostics-oms-setup.md) eller en annan logg tolknings lösning.

## <a name="log-and-event-sources"></a>Logg-och händelse källor

### <a name="service-fabric-platform-events"></a>Service Fabric plattforms händelser
Service Fabric avger några färdiga loggar via [LTTng](https://lttng.org), inklusive drift händelser eller körnings händelser. Dessa loggar lagras på den plats som klustrets Resource Manager-mall anger. Om du vill hämta eller ange information om lagrings kontot söker du efter taggen **AzureTableWinFabETWQueryable** och letar efter **StoreConnectionString**.

### <a name="application-events"></a>Program händelser
 Händelser som skickas från dina programs och tjänsters kod som anges av dig vid instrumentering av program varan. Du kan använda valfri loggnings lösning som skriver textbaserade loggfiler, till exempel LTTng. Mer information finns i LTTng-dokumentationen om att spåra ditt program.

[Övervaka och diagnostisera tjänster i en lokal dator utvecklings installation](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuera tillägget för diagnostik
Det första steget i att samla in loggar är att distribuera Diagnostics-tillägget på varje virtuell dator i Service Fabric klustret. Diagnostics-tillägget samlar in loggar på varje virtuell dator och laddar upp dem till det lagrings konto som du anger. 

Om du vill distribuera tillägget för diagnostik till de virtuella datorerna i klustret som en del av klustret skapas, ställer du in **diagnostik** på **på**. När du har skapat klustret kan du inte ändra den här inställningen med hjälp av portalen, så du måste göra lämpliga ändringar i Resource Manager-mallen.

Detta konfigurerar LAD-agenten för att övervaka de angivna loggfilerna. När en ny rad läggs till i filen skapas en syslog-post som skickas till den lagring (tabell) som du har angett.


## <a name="next-steps"></a>Nästa steg

1. Information om vilka händelser som du bör undersöka när du felsöker problem finns i [LTTng-dokumentationen](https://lttng.org/docs) och [med lad](../virtual-machines/extensions/diagnostics-linux.md).
2. [Konfigurera den Log Analytics agenten](service-fabric-diagnostics-event-analysis-oms.md) så att du kan samla in mått, övervaka behållare som distribuerats i klustret och visualisera dina loggar 
