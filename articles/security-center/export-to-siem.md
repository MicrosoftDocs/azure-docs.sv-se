---
title: Strömma dina aviseringar från Azure Security Center till SIEM-system (Security information and Event Management) och andra övervaknings lösningar
description: Lär dig att strömma dina säkerhets aviseringar till Azure Sentinel, Siem, SOAR eller ITSM-lösningar
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572948"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Strömma aviseringar till en SIEM, SOAR eller IT Service Management-lösning

Azure Security Center kan strömma dina säkerhets aviseringar till de mest populära säkerhets informations-och händelse hanterings-och händelse hanterings-och händelse hanterings-SIEM (SOAR) och IT Service Management-lösningar (ITSM).

Det finns Azure-inbyggda verktyg för att se till att du kan visa dina aviserings data i alla de mest populära lösningar som används idag, inklusive:

- **Azure Sentinel**
- **Splunk Enterprise och Splunk Cloud**
- **IBM-QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Strömma aviseringar till Azure Sentinel 

Security Center internt integreras med Azure Sentinel, Azures moln-interna SIEM och SOAR-lösning. 

[Läs mer om Azure Sentinel](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Azure Sentinel-kopplingar för Security Center

Azure Sentinel innehåller inbyggda anslutnings program för Azure Security Center på prenumerations-och klient nivåerna:

- [Strömma aviseringar till Azure Sentinel på prenumerations nivå](../sentinel/connect-azure-security-center.md)
- [Anslut alla prenumerationer i din klient organisation till Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Konfigurera inmatning av alla gransknings loggar i Azure Sentinel 

Ett annat alternativ för att undersöka Security Center aviseringar i Azure Sentinel är att strömma dina gransknings loggar till Azure Sentinel:
    - [Ansluta till Windows säkerhetshändelser](../sentinel/connect-windows-security-events.md)
    - [Samla in data från Linux-baserade källor med syslog](../sentinel/connect-syslog.md)
    - [Anslut data från Azure aktivitets logg](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel faktureras baserat på mängden data som matas in för analys i Azure Sentinel och lagras i arbets ytan Azure Monitor Log Analytics. Azure Sentinel erbjuder en flexibel och förutsägbar pris modell. [Läs mer på prissättnings sidan för Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Strömma aviseringar med Microsoft Graph Security API

Security Center har direkt integrering med Microsoft Graph Security API. Ingen konfiguration krävs och det finns inga ytterligare kostnader. 

Du kan använda detta API för att strömma aviseringar från **hela din klient organisation** (och data från många andra Microsoft-säkerhetsprodukter) till Siem från tredje part och andra populära plattformar:

- **Splunk Enterprise och Splunk Cloud**  -  [Använd Add-On Microsoft Graph Security API för Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Anslut till Microsoft Graph Security API i Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Följ anvisningarna för att installera och konfigurera Microsoft Graph Security API-programmet från ServiceNow Store](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [IBM: s enhets support modul för Azure Security Center via Microsoft Graph-API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo-nätverk**, **avvikelsei**, **Lookout**, **inspark** och fler- [Microsoft Graph säkerhets-API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Läs mer om Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Strömma aviseringar med Azure Monitor 

För att strömma varningar till **ArcSight**, **Splunk**, **SumoLogic**, Syslog-servrar, **LogRhythm**, **Logz.io** och andra övervaknings lösningar. Anslut Security Center med Azure monitor via Azure Event Hubs:

1. Aktivera [kontinuerlig export](continuous-export.md) för att strömma Security Center varningar till en dedikerad Azure Event Hub på prenumerations nivån. 
    > [!TIP]
    > Om du vill göra detta på hanterings grupps nivå med hjälp av Azure Policy, se [skapa kontinuerlig export av Automation-konfigurationer i skala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Anslut Azure Event Hub till önskad lösning med hjälp av Azure Monitor inbyggda kopplingar](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Du kan också strömma obehandlade loggar till Azure Event Hub och ansluta till din önskade lösning. Läs mer i [övervaknings data tillgängliga](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Om du vill visa händelse scheman för de exporterade data typerna kan du gå till händelse [scheman för Event Hub](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras hur du ser till att Azure Security Center aviserings data är tillgängliga i ditt SIEM-, SOAR-eller ITSM-verktyg. För relaterat material, se:

- [Vad är Azure Sentinel?](../sentinel/overview.md)
- [Aviserings validering i Azure Security Center](security-center-alert-validation.md) – verifiera att aviseringarna har kon figurer ATS korrekt
- [Exportera Security Center data kontinuerligt](continuous-export.md)