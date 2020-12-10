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
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 0a59c748dba87765537fc7c9d6382c7d2b726f43
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008069"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Konfigurera riskreduceringsrapporter och flödesloggar för DDOS-attacker 

Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. Avancerad telemetri exponeras via Azure Monitor inklusive detaljerade mått under ett DDoS-angrepp. Aviseringar kan konfigureras för alla Azure Monitor mått som visas av DDoS Protection. Loggning kan integreras ytterligare med [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Visa och konfigurera DDoS attack minsknings rapporter
> * Visa och konfigurera DDoS Attacks minskning av flödes loggar

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Visa och konfigurera DDoS attack minsknings rapporter

Rapporter om attack minskning använder de Netflow Protocol-data som sammanställs för att ge detaljerad information om angreppet på din resurs. När en offentlig IP-resurs är utsatt för angrepp startar rapportgenerering så snart som lösningen startar. Det kommer att finnas en stegvis rapport som genereras var 5: e minut och en efter minsknings rapport för hela minsknings perioden. För att säkerställa att DDoS-attacken fortsätter under en längre tid kan du Visa den senaste ögonblicks bilden av minsknings rapporten var 5: e minut och en fullständig sammanfattning när risken för attacker är över. 

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Under **Inställningar** väljer du **diagnostikinställningar**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill använda för att logga mått för.
6. Välj **Aktivera diagnostik för att samla in DDoSMitigationReports-loggen** och välj sedan så många av följande alternativ som du behöver:

    - **Arkivera till ett lagrings konto**: data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [arkivera resurs loggar](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Strömma till en Event Hub**: tillåter att en logg mottagare hämtar loggar med hjälp av en Azure Event Hub. Event Hub möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns i [strömma resurs loggar till en Event Hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Skicka till Log Analytics**: skriver loggar till tjänsten Azure Monitor. Mer information om det här alternativet finns [i samla in loggar för användning i Azure Monitor loggar](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Både den stegvisa & minsknings rapporter efter attacken innehåller följande fält
- Angrepps vektorer
- Trafik statistik
- Orsak till ignorerade paket
- Protokoll som ingår
- 10 främsta käll länder eller regioner
- ASN: er 10 främsta källa

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Visa och konfigurera DDoS Attacks minskning av flödes loggar
Med anslutnings åtgärder för attackering kan du granska förlorad trafik, vidarebefordrad trafik och andra intressanta Datapoints under en aktiv DDoS-attack i nära real tid. Du kan mata in en konstant ström från dessa data till Azure Sentinel eller till SIEM-system från tredje part via händelsehubben för övervakning i nära real tid, vidta potentiella åtgärder och åtgärda behovet av dina försvar.

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Under **Inställningar** väljer du **diagnostikinställningar**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill använda för att logga mått för.
6. Välj **Aktivera diagnostik för att samla in DDoSMitigationFlowLogs-loggen** och välj sedan så många av följande alternativ som du behöver:

    - **Arkivera till ett lagrings konto**: data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [arkivera resurs loggar](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Strömma till en Event Hub**: tillåter att en logg mottagare hämtar loggar med hjälp av en Azure Event Hub. Event Hub möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns i [strömma resurs loggar till en Event Hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Skicka till Log Analytics**: skriver loggar till tjänsten Azure Monitor. Mer information om det här alternativet finns [i samla in loggar för användning i Azure Monitor loggar](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

Med den här [mallen](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) skapas en Azure policy-definition för aktivering av diagnostisk loggning.

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel data Connector

Du kan ansluta till Azure Sentinel, Visa och analysera dina data i arbets böcker, skapa anpassade aviseringar och lägga till dem i gransknings processer. Information om hur du ansluter till Azure Sentinel finns i [Anslut till Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Azure Sentinel DDoS-anslutning](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection arbets bok

Du kan använda den här Azure Resource Manager-mallen (ARM) för att distribuera en arbets bok för attack analys. I den här arbets boken kan du alltid visualisera angrepps data över flera filter bara paneler för att enkelt förstå vad som är i spel. När du distribuerar den här ARM-mallen måste du fylla i följande:

* Namn på arbetsyta
* ResourceGroup för arbets yta
* Prenumerations-ID för arbets yta

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

Flödes loggarna innehåller följande fält: 
- Käll-IP-adress
- Mål-IP-adress
- Källport 
- Målport 
- Protokoll typ 
- Åtgärd som vidtas under minskning

![DDoS Protection arbets bok](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

Attack analys fungerar bara om DDoS Protection standard är aktiverat på det virtuella nätverket för den offentliga IP-adressen. 

## <a name="sample-log-outputs"></a>Exempel på logg utmatningar

Följande skärm bilder är exempel på logg utdata:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

- Visa och konfigurera DDoS attack minsknings rapporter
- Visa och konfigurera DDoS Attacks minskning av flödes loggar

Information om hur du testar och simulerar en DDoS-attack finns i test guide för simulering:

> [!div class="nextstepaction"]
> [Testa genom simuleringar](test-through-simulations.md)