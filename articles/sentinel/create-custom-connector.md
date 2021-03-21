---
title: Resurser för att skapa anpassade Azure Sentinel-anslutningar | Microsoft Docs
description: Lär dig mer om tillgängliga resurser för att skapa anpassade anslutningar för Azure Sentinel. Metoderna omfattar Log Analytics agent och API, Logstash, Logic Apps, PowerShell och Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724361"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Resurser för att skapa anpassade Azure Sentinel-anslutningar

Azure Sentinel tillhandahåller ett brett utbud av [inbyggda anslutningar för Azure-tjänster och externa lösningar](connect-data-sources.md)och har även stöd för inmatning av data från vissa källor utan en särskild anslutning.

Om du inte kan ansluta data källan till Azure Sentinel med någon av de befintliga lösningarna kan du överväga att skapa din egen anslutning för data källa.

En fullständig lista över anslutningar som stöds finns i [Azure Sentinel: anslutningar total (CEF, syslog, Direct, agent, Custom, och flera)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) blogg inlägg.

## <a name="compare-custom-connector-methods"></a>Jämför anpassade anslutnings metoder

I följande tabell jämförs viktig information om varje metod för att skapa anpassade anslutningar som beskrivs i den här artikeln. Välj länkarna i tabellen om du vill ha mer information om varje metod.

|Metod Beskrivning  |Funktion | Utan server    |Komplexitet  |
|---------|---------|---------|---------|
|**[Log Analytics agent](#connect-with-the-log-analytics-agent)** <br>Bäst för att samla in filer från lokala och IaaS källor   | Endast fil samling  |   Inga      |Låg         |
|**[Logstash](#connect-with-logstash)** <br>Bäst för lokala och IaaS källor, alla källor som är tillgängliga för ett plugin-program och organisationer som redan är bekanta med Logstash  | Tillgängliga plugin-program, plus anpassade plugin-program, ger stor flexibilitet.   |   Varken kräver att en virtuell dator eller ett virtuellt dator kluster körs           |   Börjar stöder många scenarier med plugin-program      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Hög kostnad; Undvik för data med hög volym <br>Bäst för moln källor med låg volym  | Kod fri programmering ger begränsad flexibilitet, utan stöd för att implementera algoritmer.<br><br> Om ingen tillgänglig åtgärd har stöd för dina krav kan du lägga till komplexa åtgärder för att skapa en anpassad åtgärd.    |    Ja         |   Börjar enkel, kod utveckling      |
|**[PowerShell](#connect-with-powershell)** <br>Bäst för prototyper och regelbundna fil överföringar | Direkt stöd för fil insamling. <br><br>PowerShell kan användas för att samla in fler källor, men kräver kodning och konfiguration av skriptet som en tjänst.      |Inga               |  Låg       |
|**[Log Analytics-API](#connect-with-the-log-analytics-api)** <br>Bäst för ISV: er som implementerar integrering och för unika krav för samlingar   | Stöder alla tillgängliga funktioner med koden.  | Beror på implementeringen           |     Högt    |
|**[Azure Functions](#connect-with-azure-functions)** Bäst för moln källor med hög volym och för unika krav för samlingar  | Stöder alla tillgängliga funktioner med koden.  |  Ja             |     Högt kräver programmerings kunskap    |
|     |         |                |

> [!TIP]
> För jämförelser av användning av Logic Apps och Azure Functions för samma koppling, se:
> 
> - [Mata in snabbt webb programs brand Väggs loggar i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub-community): [Logic app Connector](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Anslut till Log Analytics agent

Om data källan levererar händelser i filer, rekommenderar vi att du använder Azure Monitor Log Analytics agent för att skapa din anpassade anslutning.

- Mer information finns i [samla in anpassade loggar i Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Ett exempel på den här metoden finns i [samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Anslut med Logstash

Om du är bekant med [Logstash](https://www.elastic.co/logstash)kanske du vill använda Logstash med [plugin-programmet för Logstash-utdata för Azure Sentinel](connect-logstash.md) för att skapa din anpassade anslutning.

Med plugin-programmet för Azure Sentinel Logstash-utdata kan du använda valfritt Logstash-plugin-program för indata och filtrering och konfigurera Azure Sentinel som utdata för en Logstash-pipeline. Logstash har ett stort bibliotek med plugin-program som möjliggör indata från olika källor, till exempel Event Hubs, Apache Kafka, filer, databaser och moln tjänster. Använd filter-plugin-program för att analysera händelser, filtrera bort onödiga händelser, obfuscate-värden och mycket annat.

Exempel på hur du använder Logstash som en anpassad anslutning finns i:

- [Jakt för kapital ett intrång ttps i AWS-loggar med Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blogg)
- [Radware Azure Sentinel Implementation Guide](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Exempel på användbara Logstash-plugin-program finns i:

- [Plugin-program för CloudWatch-indatamängd](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs-plugin-program](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage indata-plugin](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub plugin-program](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash aktiverar även skalad data insamling med ett kluster. Mer information finns i [använda en belastningsutjämnad LOGSTASH VM i stor skala](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Anslut till Logic Apps

Använd en [Azure Logic-app](../logic-apps/index.yml) för att skapa en server lös, anpassad anslutning för Azure Sentinel.

> [!NOTE]
> När du skapar Server lösa anslutningar med Logic Apps kan det vara praktiskt att använda Logic Apps för dina anslutningar kan vara kostsamt för stora mängder data.
>
> Vi rekommenderar att du bara använder den här metoden för data källor med låg volym eller hur du kan utöka dina data överföringar.
>

1. **Använd någon av följande utlösare för att starta Logic Apps**:

    |Utlösare  |Beskrivning  |
    |---------|---------|
    |**En återkommande uppgift**     |   Du kan till exempel schemalägga din Logic app för att hämta data regelbundet från vissa filer, databaser eller externa API: er. <br>Mer information finns i [skapa, schemalägga och köra återkommande uppgifter och arbets flöden i Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**Utlösare på begäran**     | Kör din Logic app på begäran för manuell data insamling och testning. <br>Mer information finns i  [anropa, utlösa eller kapsla Logi Kap par med https-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).        |
    |**HTTP/S-slutpunkt**     |  Rekommenderas för strömning och om käll systemet kan starta data överföringen. <br>Mer information finns i [anropa tjänst slut punkter via http eller https](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Använd alla Logic app-kopplingar som läser information för att hämta dina händelser**. Exempel:

    - [Ansluta till en REST API](/connectors/custom-connectors/)
    - [Ansluta till en SQL Server](/connectors/sql/)
    - [Ansluta till ett fil system](/connectors/filesystem/)

    > [!TIP]
    > Anpassade anslutningar till REST-API: er, SQL-servrar och fil system stöder också hämtning av data från lokala data källor. Mer information finns i [Installera lokala data Gateway](/connectors/filesystem/) -dokumentation.
    >

1. **Förbered den information som du vill hämta**.

    Använd till exempel [åtgärden parsa JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) för att få åtkomst till egenskaper i JSON-innehåll, så att du kan välja dessa egenskaper från listan med dynamiskt innehåll när du anger indata för din Logic app.

    Mer information finns i [utföra data åtgärder i Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Skriv data till Log Analytics**.

    Mer information finns i dokumentationen för [Azure Log Analytics data Collector](/connectors/azureloganalyticsdatacollector/) .

Exempel på hur du kan skapa en anpassad anslutning för Azure Sentinel med Logic Apps finns i:

- [Skapa en datapipeline med data insamlings-API: et](/connectors/azureloganalyticsdatacollector/)
- [Palo Prisma Logic app Connector med en webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub community)
- [Skydda dina Microsoft Teams-anrop med schemalagd aktivering](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blogg)
- Mata [in AlienVault-OTX hot indikatorer i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blogg)
- [Skicka Proofpoint tryck på loggar till Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blogg)


## <a name="connect-with-powershell"></a>Ansluta med PowerShell

Med [PowerShell-skriptet upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) kan du använda PowerShell för att strömma händelser eller kontext information till Azure Sentinel från kommando raden. Den här strömningen skapar effektivt en anpassad anslutning mellan din data källa och Azure Sentinel.

Följande skript laddar till exempel en CSV-fil till Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[PowerShell-skriptet upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) använder följande parametrar:

|Parameter  |Beskrivning  |
|---------|---------|
|**WorkspaceId**     |   Ditt Azure Sentinel-arbetsyte-ID där du lagrar dina data.  [Hitta ditt arbetsyte-ID och nyckel](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Den primära eller sekundära nyckeln för Azure Sentinel-arbetsytan där du lagrar dina data. [Hitta ditt arbetsyte-ID och nyckel](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Namnet på den anpassade logg tabell där du vill lagra data. Ett suffix för **_CL** kommer automatiskt att läggas till i slutet av ditt tabell namn.  |
|**AddComputerName**     |   När den här parametern finns lägger skriptet till det aktuella dator namnet i alla logg poster i ett fält med namnet **dator**.      |
|**TaggedAzureResourceId**     | När den här parametern finns associerar skriptet alla överförda logg poster med den angivna Azure-resursen. <br><br>Den här kopplingen möjliggör överförda logg poster för resurs kontext frågor och följer rollbaserad åtkomst kontroll på resurs nivå.       |
|**AdditionalDataTaggingName**     |      När den här parametern finns lägger skriptet till ett annat fält till alla logg poster, med det konfigurerade namnet och det värde som har kon figurer ATS för parametern **AdditionalDataTaggingValue** . <br><br>I det här fallet får **AdditionalDataTaggingValue** inte vara tomt. |
|**AdditionalDataTaggingValue**     |   När den här parametern finns lägger skriptet till ett annat fält till alla logg poster, med det konfigurerade värdet och det fält namn som har kon figurer ATS för parametern **AdditionalDataTaggingName** . <br><br>Om parametern **AdditionalDataTaggingName** är tom, men ett värde har kon figurer ATS, är standard fält namnet **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Hitta ditt arbetsyte-ID och nyckel

Hitta information om parametrarna **WorkspaceID** och **WorkspaceKey** i Azure Sentinel:

1. I Azure Sentinel väljer du **Inställningar** till vänster och väljer sedan fliken Inställningar för **arbets yta** .

1. Under **Kom igång med Log Analytics**  >  **1 Anslut en data källa** väljer du **hantering av Windows-och Linux-agenter**.

1. Hitta ditt arbetsyte-ID, primär nyckel och sekundär nyckel på fliken **Windows-servrar** .
## <a name="connect-with-the-log-analytics-api"></a>Anslut med Log Analytics API

Du kan strömma händelser till Azure Sentinel genom att använda API: et Log Analytics data Collector för att anropa en RESTful-slutpunkt direkt.

När du anropar en RESTful-slutpunkt krävs det också mer flexibilitet när du anropar en-slutpunkt direkt.

Mer information finns i API för [Log Analytics data insamling](../azure-monitor/logs/data-collector-api.md), särskilt i följande exempel:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Anslut till Azure Functions

Använd Azure Functions tillsammans med ett RESTful-API och olika kodnings språk, till exempel [PowerShell](../azure-functions/functions-reference-powershell.md), för att skapa en server lös anpassad anslutning.

Exempel på den här metoden finns i:

- [Anslut din VMware kol Black Endpoint-standard till Azure Sentinel med Azure Function](connect-vmware-carbon-black.md)
- [Anslut dina okta enkla Sign-On till Azure Sentinel med Azure Function](connect-okta-single-sign-on.md)
- [Anslut din Proofpoint tryck till Azure Sentinel med Azure Function](connect-proofpoint-tap.md)
- [Anslut din virtuella Qualys-dator till Azure Sentinel med Azure Function](connect-qualys-vm.md)
- [Mata in XML, CSV eller andra data format](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Övervaka zoom med Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blogg)
- [Distribuera en Funktionsapp för att hämta Office 365 Management API-data till Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel GitHub community)

## <a name="parse-your-custom-connector-data"></a>Parsa dina anpassade anslutnings data

Du kan använda den anpassade kopplingens inbyggda analys metod för att extrahera relevant information och fylla i relevanta fält i Azure Sentinel.

Exempel:

- **Om du har använt Logstash** använder du [grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) filter-plugin-programmet för att analysera dina data.
- **Om du har använt en Azure-funktion** parsar du dina data med kod. Mer information finns i [tolkare](normalization.md#parsers).

Azure Sentinel stöder parsning vid tidpunkten för frågan. Vid parsning vid tidpunkten kan du skicka data i det ursprungliga formatet och sedan analysera vid behov.

Parsning vid tid innebär också att du inte behöver känna till din datas exakta struktur i förväg, när du skapar din anpassade anslutning eller till och med den information som du behöver extrahera. I stället kan du när som helst analysera dina data, även under en undersökning.

> [!NOTE]
> Uppdateringen av parsern gäller även för data som du redan har matat in i Azure Sentinel.
> 
## <a name="next-steps"></a>Nästa steg

Använd de data som matas in i Azure Sentinel för att skydda din miljö med någon av följande processer:

- [Få insyn i aviseringar](quickstart-get-visibility.md)
- [Visualisera och övervaka dina data](tutorial-monitor-your-data.md)
- [Undersöka incidenter](tutorial-investigate-cases.md)
- [Identifiera hot](tutorial-detect-threats-built-in.md)
- [Automatisera skydd mot hot](tutorial-respond-threats-playbook.md)
- [Sök efter hot](hunting.md)