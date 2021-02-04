---
title: Anslut din Akamai säkerhets händelse insamlare till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Akamai Security Events Connector för att hämta Akamai-lösningars säkerhets loggar till Azure Sentinel. Visa Akamai-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: c833d87b8d85c75c4f050f0130ddfd74342f4c52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566936"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Anslut din Akamai säkerhets händelse insamlare till Azure Sentinel

> [!IMPORTANT]
> Akamai Security Events Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Akamai för säkerhets händelser till Azure Sentinel. Med data Connector Akamai Security events kan du enkelt ansluta dina Akamai-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Integreringen mellan insamlaren av Akamai säkerhets händelser och Azure Sentinel använder CEF-formaterade syslog, en Linux-baserad logg vidarebefordrare och Log Analytics agenten. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Skicka Akamai säkerhets händelse loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du insamlaren av Akamai säkerhets händelser så att syslog-meddelanden skickas i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **Akamai Security Events (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för storleks information, mer detaljerade anvisningar och djupgående förklaring.

    1. Under **2. Vidarebefordra CEF-loggar (common Event format) till syslog-agenten** – följa Akamai-instruktioner för att [Konfigurera Siem-integrering](https://developer.akamai.com/tools/integrations/siem) och konfigurera [en CEF-anslutning](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Den här anslutnings tjänsten tar emot säkerhets händelser från dina Akamai-lösningar i nära real tid med hjälp av SIEM OPEN API och konverterar dem från JSON till CEF-format.
    
        Den här konfigurationen måste innehålla följande element:
    
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – TCP 514 (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – CEF
        - Logg typer – alla tillgängliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. Använd följande steg för att ställa in funktionen **AkamaiSIEMEvent** Kusto som ska användas i frågor och arbets böcker.

1. På navigerings menyn i Azure Sentinel väljer du **loggar**.

1. Kopiera följande fråga och klistra in den i frågefönstret.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Klicka på list rutan **Spara** och sedan på **Spara**. I panelen **Spara**

    1. Under **namn** anger du **AkamaiSIEMEvent**.

    1. Välj **funktion** under **Spara som**.

    1. Under **funktions Ali Aset** anger du **AkamaiSIEMEvent**.

    1. Under **kategori**, anger du **Functions**.

    1. Klicka på **Spara**.

    Funktions appar tar vanligt vis mellan 10 och 15 minuter att aktivera.

Nu är du redo att fråga Akamai data genom att ange `AkamaiSIEMEvent` i den översta raden i frågefönstret.

Se **Nästa steg** -fliken på kopplings sidan för fler fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Akamai säkerhets händelser till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
