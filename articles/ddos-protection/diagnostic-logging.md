---
title: Azure DDoS Protection standard rapporter och flödes loggar
description: Lär dig hur du konfigurerar rapporter och flödes loggar.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: cc5b3b85d6d13fda532da0993fa7f733126b8eae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591880"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Visa och konfigurera diagnostisk loggning för DDoS-skydd

Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. Avancerad telemetri exponeras via Azure Monitor inklusive detaljerade mått under ett DDoS-angrepp. Aviseringar kan konfigureras för alla Azure Monitor-mått som visas av DDoS Protection. Loggning kan integreras ytterligare med [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

Följande diagnostikloggar är tillgängliga för Azure DDoS Protection standard: 

- **DDoSProtectionNotifications**: aviseringar meddelar dig när en offentlig IP-resurs är utsatt för angrepp och när risken för attacker är över.
- **DDoSMitigationFlowLogs**: med hjälp av angrepps skydds loggar kan du granska tappad trafik, vidarebefordrad trafik och annan intressant Datapoints under en aktiv DDoS-attack i nära real tid. Du kan mata in en konstant ström från dessa data till Azure Sentinel eller till SIEM-system från tredje part via händelsehubben för övervakning i nära real tid, vidta potentiella åtgärder och åtgärda behovet av dina försvar.
- **DDoSMitigationReports**: rapporter om attack minskning använder de Netflow Protocol-data som sammanställs för att ge detaljerad information om angreppet på din resurs. När en offentlig IP-resurs är utsatt för angrepp startar rapportgenerering så snart som lösningen startar. Det kommer att finnas en stegvis rapport som genereras var 5: e minut och en efter minsknings rapport för hela minsknings perioden. För att säkerställa att DDoS-attacken fortsätter under en längre tid kan du Visa den senaste ögonblicks bilden av minsknings rapporten var 5: e minut och en fullständig sammanfattning när risken för attacker är över. 
- **AllMetrics**: ger alla möjliga mått som är tillgängliga under en DDoS-attack. 

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Konfigurera DDoS-diagnostikloggar, inklusive aviseringar, minsknings rapporter och åtgärder för att minska flödes loggar. 
> * Aktivera diagnostisk loggning på alla offentliga IP-adresser i ett definierat omfång.
> * Visa loggdata i arbets böcker.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md) och DDoS Protection standard måste aktive ras på ett virtuellt nätverk.
- DDoS övervakar offentliga IP-adresser tilldelade till resurser i ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) som listas i [virtuellt nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inklusive Azure Load Balancer där de virtuella datorerna finns i det virtuella nätverket), förutom för Azure App Service miljöer. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator.    

## <a name="configure-ddos-diagnostic-logs"></a>Konfigurera DDoS-diagnostikloggar

Om du vill aktivera diagnostikloggning automatiskt på alla offentliga IP-adresser i en miljö, kan du hoppa över att [Aktivera diagnostikloggning på alla offentliga IP-adresser](#enable-diagnostic-logging-on-all-public-ips).

1. Välj **Alla tjänster** längst upp till vänster i portalen.
2. Ange *Övervakare* i rutan **Filter**. Välj **Övervakare** i sökresultatet.
3. Välj **Diagnostikinställningar** under **Inställningar**.
4. Välj den **prenumeration** och **resursgrupp** som innehåller den offentliga IP-adress du vill logga.
5. Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill aktivera loggar för.
6. Välj **Lägg till diagnostikinställning**. Under **Kategoridetaljer** väljer du bland följande alternativ och väljer sedan **Spara**.

    ![DDoS diagnostikinställningar](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Under **mål information** väljer du så många av följande alternativ som du behöver:

    - **Arkivera till ett lagrings konto**: data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [arkivera resurs loggar](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Strömma till en Event Hub**: tillåter att en logg mottagare hämtar loggar med hjälp av en Azure Event Hub. Event Hub möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns i [strömma resurs loggar till en Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Skicka till Log Analytics**: skriver loggar till tjänsten Azure Monitor. Mer information om det här alternativet finns [i samla in loggar för användning i Azure Monitor loggar](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Logg scheman

I följande tabell visas fält namn och beskrivningar:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Fältnamn | Beskrivning |
| --- | --- |
| **TimeGenerated** | Datum och tid i UTC när meddelandet skapades. |
| **ResourceId** | Resurs-ID för din offentliga IP-adress. |
| **Kategori** | För aviseringar är detta `DDoSProtectionNotifications` .|
| **ResourceGroup** | Resurs gruppen som innehåller din offentliga IP-adress och det virtuella nätverket. |
| **SubscriptionId** | Ditt prenumerations-ID för DDoS Protection plan. |
| **Resurs** | Namnet på din offentliga IP-adress. |
| **ResourceType** | Detta är alltid `PUBLICIPADDRESS` . |
| **OperationName** | För aviseringar är detta `DDoSProtectionNotifications` .  |
| **Meddelande** | Information om angreppet. |
| **Typ** | Typ av meddelande. Möjliga värden är `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Din offentliga IP-adress. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Fältnamn | Beskrivning |
| --- | --- |
| **TimeGenerated** | Datum och tid i UTC när flödes loggen skapades. |
| **ResourceId** | Resurs-ID för din offentliga IP-adress. |
| **Kategori** | För flödes loggar är detta `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | Resurs gruppen som innehåller din offentliga IP-adress och det virtuella nätverket. |
| **SubscriptionId** | Ditt prenumerations-ID för DDoS Protection plan. |
| **Resurs** | Namnet på din offentliga IP-adress. |
| **ResourceType** | Detta är alltid `PUBLICIPADDRESS` . |
| **OperationName** | För flödes loggar är detta `DDoSMitigationFlowLogs` . |
| **Meddelande** | Information om angreppet. |
| **SourcePublicIpAddress** | Den offentliga IP-adressen för klienten som genererar trafik till din offentliga IP-adress. |
| **SourcePort** | Port nummer mellan 0 och 65535. |
| **DestPublicIpAddress** | Din offentliga IP-adress. |
| **DestPort** | Port nummer mellan 0 och 65535. |
| **Protokoll** | Typ av protokoll. Möjliga värden är `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Fältnamn | Beskrivning |
| --- | --- |
| **TimeGenerated** | Datum och tid i UTC när rapporten skapades. |
| **ResourceId** | Resurs-ID för din offentliga IP-adress. |
| **Kategori** | För aviseringar är detta `DDoSProtectionNotifications` .|
| **ResourceGroup** | Resurs gruppen som innehåller din offentliga IP-adress och det virtuella nätverket. |
| **SubscriptionId** | Ditt prenumerations-ID för DDoS Protection plan. |
| **Resurs** | Namnet på din offentliga IP-adress. |
| **ResourceType** | Detta är alltid `PUBLICIPADDRESS` . |
| **OperationName** | För minsknings rapporter blir detta `DDoSMitigationReports` . |
| **ReportType** | Möjliga värden är `Incremental` , `PostMitigation` .|
| **MitigationPeriodStart** | Datum och tid i UTC när minskningen startades.  |
| **MitigationPeriodEnd** | Datum och tid i UTC när minskningen avslutades. |
| **IPAddress** | Din offentliga IP-adress. |
| **AttackVectors** |  Nedbrytning av angrepps typer. Nycklarna inkluderar `TCP SYN flood` ,,, `TCP flood` `UDP flood` `UDP reflection` , `Other packet flood` .|
| **TrafficOverview** |  Nedbrytning av attack trafik. Nycklarna inkluderar,,,,,, `Total packets` `Total packets dropped` `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` `Total Other packets dropped` . |
| **Protokoll** | Nedbrytning av protokoll som ingår. Nycklarna inkluderar `TCP` , `UDP` , `Other` . |
| **DropReasons** | Analys av orsaker till ignorerade paket. Nycklarna inkluderar,,,,,, `Protocol violation invalid TCP syn` `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | Uppdelning av de 10 främsta käll länderna i inkommande trafik. |
| **TopSourceCountriesForDroppedPackets** | Analys av de 10 främsta käll länderna för attack trafik som har begränsats. |
| **TopSourceASNs** | Analys av de 10 främsta källorna med autonoma system nummer (ASN) av inkommande trafik.  |
| **SourceContinents** | Nedbrytning av käll kontinenten för inkommande trafik. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Aktivera diagnostisk loggning på alla offentliga IP-adresser

Den här [mallen](https://aka.ms/ddosdiaglogs) skapar en Azure policy-definition för att automatiskt aktivera diagnostikloggning på alla offentliga IP-loggar i ett definierat omfång.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Visa loggdata i arbets böcker

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel data Connector

Du kan ansluta loggar till Azure Sentinel, Visa och analysera dina data i arbets böcker, skapa anpassade aviseringar och lägga till dem i gransknings processer. Information om hur du ansluter till Azure Sentinel finns i [Anslut till Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Azure Sentinel DDoS-anslutning](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection arbets bok

Du kan använda [den här Azure Resource Manager-mallen (arm)](https://aka.ms/ddosworkbook) för att distribuera en arbets bok för attack analys. Med den här arbets boken kan du visualisera angrepps data över flera filter bara paneler för att enkelt förstå vad som är i spel. 

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection arbets bok](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Validera och testa

Information om hur du simulerar en DDoS-attack för att validera dina loggar finns i [validera DDoS-identifiering](test-through-simulations.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

- Konfigurera DDoS-diagnostikloggar, inklusive aviseringar, minsknings rapporter och åtgärder för att minska flödes loggar. 
- Aktivera diagnostisk loggning på alla offentliga IP-adresser i ett definierat omfång.
- Visa loggdata i arbets böcker.

Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar angrepps aviseringar.

> [!div class="nextstepaction"]
> [Visa och konfigurera aviseringar för DDoS-skydd](alerts.md)
