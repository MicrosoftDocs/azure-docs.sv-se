---
title: Autoskala i Microsoft Azure
description: Autoskala i Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726010"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Översikt över autoskalning i Microsoft Azure
Den här artikeln beskriver vad Microsoft Azure autoskalning är, dess fördelar och hur du kommer igång med det.  

Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [API Management-tjänster](../../api-management/api-management-key-concepts.md)och [Azure datautforskaren-kluster](/azure/data-explorer/).

> [!NOTE]
> Azure har två metoder för autoskalning. En äldre version av autoskalning gäller för Virtual Machines (tillgänglighets uppsättningar). Den här funktionen har begränsad support och vi rekommenderar att du migrerar till skalnings uppsättningar för virtuella datorer för snabbare och mer tillförlitligt stöd för autoskalning. En länk till hur du använder den äldre tekniken finns i den här artikeln.  
>

## <a name="what-is-autoscale"></a>Vad är autoskalning?
Med autoskalning kan du använda rätt mängd resurser för att hantera belastningen på ditt program. Det gör att du kan lägga till resurser för att hantera ökad belastning och även spara pengar genom att ta bort resurser som är inaktiva. Du anger ett minsta och högsta antal instanser som ska köras och lägga till eller ta bort virtuella datorer automatiskt baserat på en uppsättning regler. Om du har ett minimum ser du till att programmet alltid körs, även under ingen belastning. Det maximala antalet möjliga Tim kostnader är begränsat. Du skalar automatiskt mellan dessa två extrema med regler som du skapar.

 ![Autoskalning förklaring. Lägga till och ta bort virtuella datorer](./media/autoscale-overview/AutoscaleConcept.png)

När regel villkoren är uppfyllda utlöses en eller flera automatiska skalnings åtgärder. Du kan lägga till och ta bort virtuella datorer eller utföra andra åtgärder. I följande konceptuella diagram visas den här processen.  

 ![Flödes diagram för autoskalning](./media/autoscale-overview/Autoscale_Overview_v4.png)

Följande förklaring gäller för delarna i föregående diagram.   

## <a name="resource-metrics"></a>Resursmått
Resurserna genererar Mät värden, dessa mått hanteras senare av regler. Måtten kommer via olika metoder.
Skalnings uppsättningar för virtuella datorer använder telemetridata från Azure Diagnostics-agenter medan telemetri för webbappar och moln tjänster kommer direkt från Azure-infrastrukturen. En viss statistik som används ofta är processor användning, minnes användning, antal trådar, Kölängd och disk användning. En lista över vilka telemetridata du kan använda finns i [Autoskala vanliga mått](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Anpassade mått
Du kan också använda dina egna anpassade mått som dina program kan skickas till. Om du har konfigurerat dina program så att de skickar mått till Application Insights kan du utnyttja dessa mått för att fatta beslut om att skala eller inte.

## <a name="time"></a>Tid
Schemabaserade regler baseras på UTC. Du måste ange din tidszon korrekt när du konfigurerar reglerna.  

## <a name="rules"></a>Regler
Diagrammet visar bara en regel för autoskalning, men du kan ha många av dem. Du kan skapa komplexa överlappande regler om det behövs för din situation.  Regel typer inkluderar  

* **Metric-baserad** – till exempel kan du utföra den här åtgärden när CPU-användningen är över 50%.
* **Tidsbaserad** – till exempel utlöser en webhook varje 8.00 på lördag i en specifik tidszon.

Metric-baserade regler mäter program belastning och lägger till eller tar bort virtuella datorer baserat på den belastningen. Med hjälp av schemabaserade regler kan du skala när du ser tids mönster i belastningen och vill skala innan en eventuell belastnings ökning eller minskning inträffar.  

## <a name="actions-and-automation"></a>Åtgärder och automatisering
Regler kan utlösa en eller flera typer av åtgärder.

* **Skala** virtuella datorer in eller ut
* **E-post** – skicka e-post till prenumerations administratörer, medadministratörer och/eller ytterligare e-postadress som du anger
* **Automatisera via Webhooks** – anropa Webhooks, vilket kan utlösa flera komplexa åtgärder i eller utanför Azure. I Azure kan du starta en Azure Automation Runbook, Azure Function eller Azure Logic app. Exempel-URL från tredje part utanför Azure inkluderar tjänster som slack och Twilio.

## <a name="autoscale-settings"></a>Autoskalningsinställningar
Använd följande terminologi och struktur för autoskalning.

- Den automatiska **skalnings inställningen** läses av autoskalning-motorn för att avgöra om du vill skala upp eller ned. Den innehåller en eller flera profiler, information om mål resursen och meddelande inställningar.

  - En **profil för autoskalning** är en kombination av:

    - **kapacitets inställning**, som anger lägsta, högsta och standardvärden för antal instanser.
    - **uppsättning regler** som innehåller en utlösare (tid eller mått) och en skalnings åtgärd (uppåt eller nedåt).
    - **upprepning**, som anger när den här profilen ska börja gälla.

      Du kan ha flera profiler, vilket gör att du kan ta hand om olika överlappande krav. Du kan till exempel ha olika profiler för autoskalning för olika tider på dagen eller vecko dagar.

  - En **meddelande inställning** definierar vilka meddelanden som ska inträffa när en autoskalning-händelse inträffar baserat på att uppfylla villkoren i en av profilerna för autoskalningsinställning. Autoskalning kan meddela en eller flera e-postadresser eller ringa till en eller flera Webhooks.


![Azures skalnings inställning, profil och regel struktur](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Den fullständiga listan över konfigurerbara fält och beskrivningar finns i REST API för [autoskalning](/rest/api/monitor/autoscalesettings).

Kod exempel finns i

* [Avancerad automatisk skalnings konfiguration med Resource Manager-mallar för VM Scale Sets](autoscale-virtual-machine-scale-sets.md)  
* [Skala REST API](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Horisontell vs vertikal skalning
Autoskalning skalas bara vågrätt, vilket är en ökning ("out") eller minskning ("i") i antalet VM-instanser.  Horisontellt är mer flexibelt i en moln situation eftersom det gör att du kan köra potentiellt tusentals virtuella datorer för att hantera belastningen.

Den lodräta skalningen är däremot annorlunda. Det behåller samma antal virtuella datorer, men gör de virtuella datorerna mer ("upp") eller mindre ("nere") kraftfulla. Kraften mäts i minnet, CPU-hastigheten, disk utrymmet osv.  Vertikal skalning har fler begränsningar. Det beror på tillgängligheten för större maskin vara, vilket snabbt når en övre gräns och kan variera beroende på region. Vertikal skalning kräver vanligt vis att en virtuell dator stoppas och startas om.

## <a name="methods-of-access"></a>Åtkomst metoder
Du kan ställa in autoskalning via

* [Azure-portalen](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [Plattformsoberoende kommandoradsgränssnitt (CLI):](../cli-samples.md#autoscale)
* [REST-API:et för Azure Monitor](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Tjänster som stöds för autoskalning
| Tjänst | Schema & dokument |
| --- | --- |
| Web Apps |[Skalnings Web Apps](autoscale-get-started.md) |
| Cloud Services |[Autoskala en moln tjänst](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klassisk |[Skala klassiska tillgänglighets uppsättningar för virtuella datorer](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Virtual Machines: Windows Scale Sets |[Skala skalnings uppsättningar för virtuella datorer i Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: Linux Scale Sets |[Skala skalnings uppsättningar för virtuella datorer i Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: Windows-exempel |[Avancerad automatisk skalnings konfiguration med Resource Manager-mallar för VM Scale Sets](autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Skala upp en app i Azure App-tjänsten](../../app-service/manage-scale-up.md)|
| API Management tjänst|[Skala en Azure API Management-instans automatiskt](../../api-management/api-management-howto-autoscale.md)
| Azure Datautforskaren-kluster|[Hantera skalning av Azure Datautforskaren-kluster för att hantera ändring efter frågan](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[Lägga till integrerings tjänst miljö (ISE)-kapacitet](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[Konfigurera autoskalning för mikrotjänstprogram](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Uppdatera meddelande enheter automatiskt i ett Azure Service Bus namn område](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om autoskalning använder du genom gången av autoskalning som listas tidigare eller hänvisar till följande resurser:

* [Azure Monitor vanliga mått för autoskalning](autoscale-common-metrics.md)
* [Metodtips för autoskalning i Azure Monitor](autoscale-best-practices.md)
* [Använda åtgärder för autoskalning för att skicka aviseringar via e-post och webhook](autoscale-webhook-email.md)
* [Skala REST API](/rest/api/monitor/autoscalesettings)
* [Felsöka Virtual Machine Scale Sets autoskalning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)