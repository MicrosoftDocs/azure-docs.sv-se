---
title: Övervaka åtgärder, händelser och räknare för en offentlig belastningsutjämnare
titleSuffix: Azure Load Balancer
description: Lär dig hur du aktiverar loggning för Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709826"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Monitor loggar för Azure Standard Load Balancer

Du kan använda olika typer av Azure Monitor loggar för att hantera och felsöka Azure Standard Load Balancer. Loggar kan strömmas till en händelsehubben eller en arbets yta för Log Analytics. Du kan extrahera alla loggar från Azure Blob Storage och visa dem i verktyg som Excel och Power BI. 

Typerna av loggar är:

* **Aktivitets loggar:** Du kan visa all aktivitet som skickas till dina Azure-prenumerationer, tillsammans med deras status. Mer information finns i [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md). Aktivitets loggar är aktiverade som standard och kan visas i Azure Portal. De här loggarna är tillgängliga för både Azure Basic-Load Balancer och Standard Load Balancer.
* **Standard Load Balancer mått:** Du kan använda den här loggen för att fråga de mått som exporteras som loggar för Standard Load Balancer. Dessa loggar är endast tillgängliga för Standard Load Balancer.

> [!IMPORTANT]
> Hälso avsökning och Load Balancer varnings händelse loggar fungerar inte för närvarande och visas i de [kända problemen för Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Loggar är endast tillgängliga för resurser som distribueras i Azure Resource Manager distributions modell. Du kan inte använda loggar för resurser i den klassiska distributions modellen. Mer information om distributions modellerna finns i [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivera loggning

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Aktivera loggning av händelse-och hälso avsökning för att börja samla in data som är tillgängliga via dessa loggar. Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har en belastningsutjämnare måste du [skapa en belastningsutjämnare](./quickstart-load-balancer-standard-public-portal.md) innan du fortsätter.
1. I portalen väljer du **resurs grupper**.
2. Välj **\<resource-group-name>** var belastningsutjämnaren är.
3. Välj din lastbalanserare.
4. Välj   >  **diagnostiska inställningar** för aktivitets loggen.
5. I fönstret **diagnostikinställningar** , under **diagnostikinställningar**, väljer du **+ Lägg till diagnostisk inställning**.
6. I fönstret Skapa **diagnostikinställningar** anger du **MyLBDiagnostics** i rutan **namn** .
7. Det finns tre alternativ för **diagnostikinställningar**. Du kan välja en, två eller alla tre och konfigurera var och en för dina behov:

   * **Arkivera till ett lagrings konto**. Du behöver ett lagrings konto som redan har skapats för den här processen. Om du vill skapa ett lagringskonto kan du läsa mer i [Skapa ett lagringskonto](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Markera kryss rutan **arkivera till ett lagrings konto** .
     2. Välj **Konfigurera** för att öppna fönstret **Välj ett lagrings konto** .
     3. I list rutan **prenumeration** väljer du den prenumeration där ditt lagrings konto skapades.
     4. I list rutan **lagrings konto** väljer du namnet på ditt lagrings konto.
     5. Välj **OK**.

   * **Strömma till en händelsehubben**. Du behöver en Event Hub som redan har skapats för den här processen. Information om hur du skapar en Event Hub finns i [snabb start: skapa en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md).
     1. Markera kryssrutan **Strömma till en händelsehubb**.
     2. Välj **Konfigurera** för att öppna fönstret **Välj händelsehubben** .
     3. I list rutan **prenumeration** väljer du den prenumeration där händelsehubben skapades.
     4. I list rutan **Välj Event Hub-namnrymd** väljer du namn området.
     5. I list rutan **Välj princip namn för händelse hubb** väljer du namnet.
     6. Välj **OK**.

   * **Skicka till Log Analytics**. Du behöver redan ha en Log Analytics-arbetsyta skapad och konfigurerad för den här processen. Om du vill skapa en Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
     1. Markera kryssrutan **Skicka till Log Analytics**.
     2. I list rutan **prenumeration** väljer du den prenumeration där Log Analytics arbets ytan är.
     3. I list rutan **Log Analytics arbets yta** väljer du arbets ytan.

8. I avsnittet **mått** i fönstret **diagnostikinställningar** väljer du kryss rutan **AllMetrics** .

9. Kontrol lera att allting ser korrekt ut och välj sedan **Spara** överst i fönstret Skapa **diagnostikinställningar** .

## <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Aktivitets loggen skapas som standard. Du kan konfigurera den så att den exporteras på en prenumerations nivå genom att [följa anvisningarna i den här artikeln](../azure-monitor/essentials/activity-log.md). Läs mer om dessa loggar genom att läsa artikeln [Visa aktivitets loggar för att övervaka åtgärder i resurser](../azure-resource-manager/management/view-activity-logs.md) .

Du kan visa och analysera aktivitets logg data med någon av följande metoder:

* **Azure-verktyg:** Hämta information från aktivitets loggen via Azure PowerShell, Azure CLI, Azure-REST API eller Azure Portal. Artikeln [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md) innehåller stegvisa instruktioner för varje metod.
* **Power BI:** Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing) konto kan du prova det kostnads fritt. Genom att använda [Azures gransknings loggs integrering för Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)kan du analysera dina data med förkonfigurerade instrument paneler. Du kan också anpassa vyer så att de passar dina behov.

## <a name="view-and-analyze-metrics-as-logs"></a>Visa och analysera mått som loggar
Genom att använda export funktionen i Azure Monitor kan du exportera Load Balancer mått. Dessa mått genererar en loggpost för varje samplings intervall på en minut.

Mått till-loggar-export är aktiverat på per-resurs-nivå. Så här aktiverar du loggarna:

1. Gå till fönstret **diagnostiska inställningar** .
1. Filtrera efter resurs grupp och välj sedan den Load Balancer-instans som du vill aktivera mått export för. 
1. När sidan diagnostikinställningar för Load Balancer är upp väljer du **AllMetrics** för att exportera kvalificerade mått som loggar.

Begränsningar för mått export finns i avsnittet [begränsningar](#limitations) i den här artikeln.

När du har aktiverat **AllMetrics** i diagnostikinställningar för standard Load Balancer, om du använder en Event Hub-eller Log Analytics-arbetsyta, fylls dessa loggar i tabellen **AzureMonitor** . 

Om du exporterar till Storage ansluter du till ditt lagrings konto och hämtar JSON-logg poster för händelse-och hälso avsöknings loggar. När du har hämtat JSON-filerna kan du konvertera dem till CSV och visa dem i Excel, Power BI eller andra verktyg för data visualisering. 

> [!TIP]
> Om du är bekant med Visual Studio och grundläggande begrepp för att ändra värden för konstanter och variabler i C#, kan du använda [verktygen för logg konverterare](https://github.com/Azure-Samples/networking-dotnet-log-converter) som är tillgängliga från GitHub.

## <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
När diagnostikinformation strömmas till en Event Hub kan du använda den för centraliserad logg analys i ett partner SIEM-verktyg med Azure Monitor-integrering. Mer information finns i [Stream Azure Monitoring data to Event Hub](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Skicka till Log Analytics
Du kan skicka diagnostisk information för resurser i Azure direkt till en Log Analytics-arbetsyta. På arbets ytan kan du köra komplexa frågor mot informationen för fel sökning och analys. Mer information finns i [samla in Azures resurs loggar i en Log Analytics arbets yta i Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Begränsningar
Export funktionen mått till-loggar för Azure Load Balancer har följande begränsningar:
* Mått visas för närvarande genom interna namn när de exporteras som loggar. Du kan hitta mappningen i tabellen nedan.
* Måttets dimensionalitet bevaras inte. Till exempel, med mått som **DipAvailability** (status för hälso avsökning), kommer du inte att kunna dela eller Visa IP-adressen för backend-servern.
* Mått för använda SNAT-portar och allokerade SNAT-portar är för närvarande inte tillgängliga för export som loggar.

## <a name="next-steps"></a>Nästa steg
* [Granska tillgängliga mått för belastningsutjämnaren](./load-balancer-standard-diagnostics.md)
* [Skapa och testa frågor genom att följa Azure Monitor-instruktioner](../azure-monitor/logs/log-query-overview.md)