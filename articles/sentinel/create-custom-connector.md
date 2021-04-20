---
title: Resurser för att Azure Sentinel anpassade anslutningsappar | Microsoft Docs
description: Läs mer om tillgängliga resurser för att skapa anpassade anslutningsappar för Azure Sentinel. Metoderna omfattar Log Analytics-agenten och API:et, Logstash, Logic Apps, PowerShell och Azure Functions.
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
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738908"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Resurser för att Azure Sentinel anpassade anslutningsappar

Azure Sentinel tillhandahåller en mängd inbyggda anslutningsappar för [Azure-tjänster](connect-data-sources.md)och externa lösningar, och har även stöd för inmatning av data från vissa källor utan en dedikerad anslutningsapp.

Om du inte kan ansluta datakällan till en Azure Sentinel någon av de befintliga lösningarna kan du skapa en egen anslutningsapp för datakällor.

En fullständig lista över anslutningsappar som stöds finns i blogginlägget [Azure Sentinel: The connectors grand (CEF, Syslog, Direct, Agent, Custom med](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) mera).

## <a name="compare-custom-connector-methods"></a>Jämför metoder för anpassade anslutningsappar

I följande tabell jämförs viktig information om varje metod för att skapa anpassade anslutningsappar som beskrivs i den här artikeln. Välj länkarna i tabellen för mer information om varje metod.

|Metodbeskrivning  |Funktion | Utan server    |Komplexitet  |
|---------|---------|---------|---------|
|**[Log Analytics-agent](#connect-with-the-log-analytics-agent)** <br>Bäst för att samla in filer från lokala källor och IaaS-källor   | Endast filinsamling  |   No      |Låg         |
|**[Logstash](#connect-with-logstash)** <br>Bäst för lokala och IaaS-källor, alla källor som ett plugin-program är tillgängligt för och organisationer som redan är bekanta med Logstash  | Tillgängliga plugin-program, samt anpassade plugin-program, ger stor flexibilitet.   |   Nej; kräver att en virtuell dator eller ett virtuellt datorkluster körs           |   Låg; stöder många scenarier med plugin-program      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Hög kostnad; undvik för data med stora volymer <br>Bäst för molnkällor med låg volym  | Kodlös programmering ger begränsad flexibilitet, utan stöd för implementering av algoritmer.<br><br> Om ingen tillgänglig åtgärd redan har stöd för dina krav kan det öka komplexiteten om du skapar en anpassad åtgärd.    |    Yes         |   Låg; enkel, kodlös utveckling      |
|**[PowerShell](#connect-with-powershell)** <br>Bäst för prototyper och periodiska filuppladdningar | Direkt stöd för filinsamling. <br><br>PowerShell kan användas för att samla in fler källor, men kräver kodning och konfiguration av skriptet som en tjänst.      |No               |  Låg       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>Bäst för ISV:er som implementerar integrering och för unika samlingskrav   | Stöder alla funktioner som är tillgängliga med koden.  | Beror på implementeringen           |     Högt    |
|**[Azure Functions](#connect-with-azure-functions)** Bäst för molnkällor med stora volymer och för unika samlingskrav  | Stöder alla funktioner som är tillgängliga med koden.  |  Yes             |     Hög; kräver programmeringskunskaper    |
|     |         |                |

> [!TIP]
> Jämförelser av hur du använder Logic Apps och Azure Functions för samma anslutningsapp finns i:
> 
> - [Mata in loggar Web Application Firewall snabbt till Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub-community): [Logic Anslutningsverktyg](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function-anslutningsapp](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Ansluta med Log Analytics-agenten

Om din datakälla levererar händelser i filer rekommenderar vi att du använder log analytics Azure Monitor agenten för att skapa din anpassade anslutningsapp.

- Mer information finns i Samla [in anpassade loggar i Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Ett exempel på den här metoden finns i [Samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Ansluta med Logstash

Om du är bekant med [Logstash](https://www.elastic.co/logstash)kanske du vill använda Logstash med [plugin-programmet Logstash-utdata](connect-logstash.md) för att Azure Sentinel skapa din anpassade anslutningsapp.

Med Azure Sentinel Logstash Output-plugin-programmet kan du använda valfria Logstash-plugin-program för indata och filtrering och konfigurera Azure Sentinel som utdata för en Logstash-pipeline. Logstash har ett stort bibliotek med plugin-program som möjliggör indata från olika källor, till exempel Event Hubs, Apache Kafka, filer, databaser och molntjänster. Använd filtrerings-plugin-program för att parsa händelser, filtrera onödiga händelser, fördrå värden med mera.

Exempel på hur du använder Logstash som en anpassad anslutningsapp finns i:

- [Jakt på TTPs för Capital One-intrång i AWS-loggar med Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blogg)
- [Implementeringsguide Azure Sentinel radware-program](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Exempel på användbara Logstash-plugin-program finns i:

- [Plugin-program för Cloudwatch-indata](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs plugin-program](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plugin-program för Google Cloud Storage-indata](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub plugin-program för indata](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash möjliggör även skalad datainsamling med hjälp av ett kluster. Mer information finns i Använda [en belastningsutjämnad virtuell Logstash-dator i stor skala.](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)
>

## <a name="connect-with-logic-apps"></a>Ansluta med Logic Apps

Använd en [Azure-logikapp](../logic-apps/index.yml) för att skapa en serverlös, anpassad anslutningsapp för Azure Sentinel.

> [!NOTE]
> Det kan vara praktiskt att skapa serverlösa Logic Apps, men att använda Logic Apps för dina anslutningsappar kan vara dyrt för stora mängder data.
>
> Vi rekommenderar att du endast använder den här metoden för datakällor med låg volym eller utökar dina datauppladdningar.
>

1. **Använd någon av följande utlösare för att starta Logic Apps**:

    |Utlösare  |Description  |
    |---------|---------|
    |**En återkommande uppgift**     |   Du kan till exempel schemalägga logikappen så att den hämtar data regelbundet från specifika filer, databaser eller externa API:er. <br>Mer information finns i [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden i Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**Utlösare på begäran**     | Kör logikappen på begäran för manuell datainsamling och testning. <br>Mer information finns i [Anropa, utlösa eller kapsla logikappar med HTTPS-slutpunkter.](../logic-apps/logic-apps-http-endpoint.md)        |
    |**HTTP/S-slutpunkt**     |  Rekommenderas för strömning och om källsystemet kan starta dataöverföringen. <br>Mer information finns i Anropa [tjänstslutpunkter via HTTP eller HTTPs.](../connectors/connectors-native-http.md)       |
    |     |         |

1. **Använd någon av Logic App-anslutningsapparna som läser information för att hämta dina händelser.** Exempel:

    - [Ansluta till en REST API](/connectors/custom-connectors/)
    - [Ansluta till en SQL Server](/connectors/sql/)
    - [Ansluta till ett filsystem](/connectors/filesystem/)

    > [!TIP]
    > Anpassade anslutningsappar till REST-API:er, SQL-servrar och filsystem stöder också hämtning av data från lokala datakällor. Mer information finns i Install on-premises data gateway documentation (Installera [lokal datagateway).](/connectors/filesystem/)
    >

1. **Förbered den information som du vill hämta.**

    Använd till exempel [åtgärden parsa JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) för att komma åt egenskaper i JSON-innehåll, så att du kan välja dessa egenskaper från listan med dynamiskt innehåll när du anger indata för logikappen.

    Mer information finns i [Utföra dataåtgärder i Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Skriv data till Log Analytics**.

    Mer information finns i dokumentationen [för Azure Log Analytics Data Collector.](/connectors/azureloganalyticsdatacollector/)

Exempel på hur du kan skapa en anpassad anslutningsapp för Azure Sentinel med Logic Apps finns i:

- [Skapa en datapipeline med API:et för datainsamling](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic Anslutningsverktyg med en webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub-community)
- [Skydda dina Microsoft Teams-anrop med schemalagd aktivering](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blogg)
- [Mata in CyberVault OTX-hotindikatorer i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blogg)

## <a name="connect-with-powershell"></a>Ansluta med PowerShell

Med [PowerShell-skriptet Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) kan du använda PowerShell för att strömma händelser eller kontextinformation för att Azure Sentinel från kommandoraden. Den här direktuppspelningen skapar effektivt en anpassad anslutningsapp mellan datakällan och Azure Sentinel.

Följande skript laddar till exempel upp en CSV-fil till Azure Sentinel:

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

[PowerShell-skriptskriptet Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) använder följande parametrar:

|Parameter  |Beskrivning  |
|---------|---------|
|**WorkspaceId**     |   Ditt Azure Sentinel arbetsyte-ID, där du kommer att lagra dina data.  [Hitta ditt arbetsyte-ID och din nyckel.](#find-your-workspace-id-and-key)  |
|**WorkspaceKey**     |   Den primära eller sekundära nyckeln för Azure Sentinel arbetsyta där du lagrar dina data. [Hitta ditt arbetsyte-ID och din nyckel.](#find-your-workspace-id-and-key)  |
|**LogTypeName**     |    Namnet på den anpassade loggtabell där du vill lagra data. Ett suffix **_CL** läggs automatiskt till i slutet av tabellnamnet.  |
|**AddComputerName**     |   När den här parametern finns lägger skriptet till det aktuella datornamnet i varje loggpost i ett fält med namnet **Dator**.      |
|**TaggedAzureResourceId**     | När den här parametern finns associerar skriptet alla uppladdade loggposter med den angivna Azure-resursen. <br><br>Den här associationen möjliggör överladdade loggposter för resurskontextfrågor och följer resurscentrerad, rollbaserad åtkomstkontroll.       |
|**AdditionalDataTaggingName**     |      När den här parametern finns lägger skriptet till ytterligare ett fält i varje loggpost, med det konfigurerade namnet och värdet som har konfigurerats för parametern **AdditionalDataTaggingValue.** <br><br>I det här fallet **får AdditionalDataTaggingValue** inte vara tomt. |
|**AdditionalDataTaggingValue**     |   När den här parametern finns lägger skriptet till ytterligare ett fält i varje loggpost, med det konfigurerade värdet och fältnamnet som konfigurerats för parametern **AdditionalDataTaggingName.** <br><br>Om **parametern AdditionalDataTaggingName** är tom, men ett värde har konfigurerats, är standardfältnamnet **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Hitta ditt arbetsyte-ID och din nyckel

Hitta information om parametrarna **WorkspaceID** och **WorkspaceKey** i Azure Sentinel:

1. I Azure Sentinel väljer **du Inställningar** till vänster och sedan fliken **Arbetsyteinställningar.**

1. Under **Kom igång med Log Analytics**  >  **1 Anslut en datakälla väljer** du Hantering av **Windows- och Linux-agenter.**

1. Hitta ditt arbetsyte-ID, primärnyckel och sekundärnyckel på flikarna **Windows-servrar.**
## <a name="connect-with-the-log-analytics-api"></a>Ansluta med Log Analytics-API:et

Du kan strömma händelser till Azure Sentinel med hjälp av Log Analytics Data Collector-API:et för att anropa en RESTful-slutpunkt direkt.

När du anropar en RESTful-slutpunkt direkt krävs mer programmering, men det ger också mer flexibilitet.

Mer information finns i [API:et för Log Analytics-datainsamlare,](../azure-monitor/logs/data-collector-api.md)särskilt i följande exempel:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Ansluta med Azure Functions

Använd Azure Functions tillsammans med ett RESTful-API och olika kodningsspråk, till exempel [PowerShell](../azure-functions/functions-reference-powershell.md), för att skapa en serverlös anpassad anslutningsapp.

Exempel på den här metoden finns i:

- [Anslut din VMware Carbon Black Cloud Endpoint Standard till Azure Sentinel med Azure Function](connect-vmware-carbon-black.md)
- [Ansluta din okta-Sign-On till Azure Sentinel med Azure Function](connect-okta-single-sign-on.md)
- [Ansluta Proofpoint TAP till Azure Sentinel med Azure Function](connect-proofpoint-tap.md)
- [Ansluta din virtuella Qualys-dator Azure Sentinel med Azure Function](connect-qualys-vm.md)
- [Mata in XML, CSV eller andra dataformat](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Övervaka Zoom med Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blogg)
- [Distribuera en funktionsapp för att hämta Office 365 API för hantering data till Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel GitHub-communityn)

## <a name="parse-your-custom-connector-data"></a>Parsa data för din anpassade anslutningsapp

Du kan använda den inbyggda parsningstekniken för din anpassade anslutningsapp för att extrahera relevant information och fylla i relevanta fält i Azure Sentinel.

Exempel:

- **Om du har använt Logstash använder du** [plugin-programmet Grok-filter](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) för att parsa dina data.
- **Om du har använt en Azure-funktion** parsar du dina data med kod. Mer information finns i [Parsers](normalization.md#parsers).

Azure Sentinel stöder parsning vid frågetid. Med parsning vid frågetiden kan du skicka data i det ursprungliga formatet och sedan parsa på begäran när det behövs.

Parsning vid frågetiden innebär också att du inte behöver känna till dina datas exakta struktur i förväg, när du skapar din anpassade anslutningsapp eller ens den information som du behöver extrahera. Parsa istället dina data när som helst, även under en undersökning.

> [!NOTE]
> Uppdatering av parsern gäller även för data som du redan har matat in i Azure Sentinel.
> 
## <a name="next-steps"></a>Nästa steg

Använd de data som matas Azure Sentinel för att skydda din miljö med någon av följande processer:

- [Få insyn i aviseringar](quickstart-get-visibility.md)
- [Visualisera och övervaka dina data](tutorial-monitor-your-data.md)
- [Undersöka incidenter](tutorial-investigate-cases.md)
- [Identifiera hot](tutorial-detect-threats-built-in.md)
- [Automatisera skydd mot hot](tutorial-respond-threats-playbook.md)
- [Sök efter hot](hunting.md)
