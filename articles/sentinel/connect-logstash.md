---
title: Anslut data källor via Logstash till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Logstash för att vidarebefordra loggar från externa data källor till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: d388478fb3bc9b4e355d8c3cd3f16c0a785b8b27
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578917"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Använd Logstash för att ansluta data källor till Azure Sentinel

> [!IMPORTANT]
> Data inmatning med Logstash output-plugin-programmet är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med hjälp av Azure Sentinels nya plugin-program för **data insamlings motorn för Logstash** kan du nu skicka vilken typ av logg du vill via Logstash direkt till din Log Analytics arbets yta i Azure Sentinel. Loggarna skickas till en anpassad tabell som du definierar med hjälp av plugin-programmet för utdata.

Mer information om hur du arbetar med Logstash data insamlings motor finns i [komma igång med Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Översikt

### <a name="architecture-and-background"></a>Arkitektur och bakgrund

![Diagram över logg tokens arkitekturen.](./media/connect-logstash/logstash-architecture.png)

Logstash-motorn består av tre komponenter:

- Plugin-program för indata: anpassad insamling av data från olika källor.
- Filtrera plugin-program: manipulering och normalisering av data enligt angivna villkor.
- Utgående plugin-program: anpassad sändning av insamlade och bearbetade data till olika destinationer.

> [!NOTE]
> Azure Sentinel stöder endast det angivna plugin-programmet för utdata. Det stöder inte plugin-program från tredje part för Azure Sentinel eller något annat Logstash-plugin-program av valfri typ.

Plugin-programmet för Azure Sentinel-utdata för Logstash skickar JSON-formaterade data till din Log Analytics-arbetsyta med Log Analytics http-insamlaren REST API. Data matas in i anpassade loggar.

- Läs mer om [Log Analytics REST API](/rest/api/loganalytics/create-request).
- Läs mer om [anpassade loggar](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Distribuera plugin-programmet för Azure Sentinel-utdata i Logstash

### <a name="step-1-installation"></a>Steg 1: installation

Plugin-programmet för Azure Sentinel-utdata är tillgängligt i Logstash-samlingen.

- Följ instruktionerna i Logstash [work with plugin](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) -dokument för att installera plugin-programmet ***[Microsoft-Logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** .
   
- Om ditt Logstash-system inte har Internet åtkomst följer du anvisningarna i dokumentet Logstash [Offline-plugin-hantering](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) för att förbereda och använda ett offline-plugin-paket. (Detta kräver att du skapar ett annat Logstash-system med Internet åtkomst.)

### <a name="step-2-configuration"></a>Steg 2: konfiguration

Använd informationen i Logstash- [strukturen i ett konfigurations fil](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) dokument och Lägg till plugin-programmet för Azure Sentinel-utdata i konfigurationen med följande nycklar och värden. (Rätt syntax för config-filen visas efter tabellen.)

| Fältnamn | Datatyp | Description |
|----------------|---------------|-----------------|
| `workspace_id` | sträng | Ange arbets ytans ID-GUID. * |
| `workspace_key` | sträng | Ange arbets ytans primära nyckel-GUID. * |
| `custom_log_table_name` | sträng | Ange namnet på den tabell där loggarna ska matas in. Det går bara att konfigurera ett tabell namn per utdatafil-plugin-program. Tabellen logg visas i Azure Sentinel under **loggar** i **tabeller** i kategorin **anpassade loggar** med ett `_CL` suffix. |
| `endpoint` | sträng | Valfritt fält. Som standard är detta Log Analytics slut punkten. Använd det här fältet om du vill ange en alternativ slut punkt. |
| `time_generated_field` | sträng | Valfritt fält. Den här egenskapen åsidosätter standard fältet **TimeGenerated** i Log Analytics. Ange namnet på tidsstämpel-fältet i data källan. Data i fältet måste följa ISO 8601-formatet ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | matris | Ange en lista med Log Analytics utmatnings schema fält. Varje List objekt ska omges av enkla citat tecken och objekten avgränsade med kommatecken, och hela listan omges av hakparenteser. Se exemplet nedan. |
| `plugin_flush_interval` | antal | Valfritt fält. Ange för att definiera det maximala intervallet (i sekunder) mellan meddelande överföring till Log Analytics. Standardvärdet är 5. |
    | `amount_resizing` | boolean | Sant eller falskt. Aktivera eller inaktivera funktionen för automatisk skalning, vilket justerar storleken på meddelandets buffertstorlek enligt volymen av loggdata som tagits emot. |
| `max_items` | antal | Valfritt fält. Gäller endast om `amount_resizing` värdet är "false". Används för att ange ett tak för buffertstorleken (i poster). Standardvärdet är 2 000.  |

\* Du kan hitta arbetsyte-ID: t och primär nyckeln i arbets ytans resurs under **agent hantering**.

#### <a name="sample-configurations"></a>Exempel på konfigurationer

Här följer några exempel på konfigurationer som använder några olika alternativ.

- En grundläggande konfiguration som använder en filebeat-ingångs pipe:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- En grundläggande konfiguration som använder en TCP-ingångs pipe:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- En avancerad konfiguration:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Besök [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) för utdata för att lära dig mer om dess interna arbete, konfiguration och prestanda inställningar.

### <a name="step-3-restart-logstash"></a>Steg 3: starta om Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Steg 4: Visa inkommande loggar i Azure Sentinel

1. Verifiera att meddelanden skickas till plugin-programmet för utdata.

1. Klicka på **loggar** på navigerings menyn i Azure Sentinel. Under rubriken **tabeller** expanderar du kategorin **anpassade loggar** . Leta upp och klicka på namnet på den tabell som du har angett (med ett `_CL` suffix) i konfigurationen.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Skärm bild av anpassade loggar token-loggar.":::

1. Om du vill se poster i tabellen frågar du tabellen med hjälp av tabell namnet som schema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Skärm bild av en logg token anpassade loggar fråga.":::

## <a name="monitor-output-plugin-audit-logs"></a>Övervaka gransknings loggar för utdata-plugin

Om du vill övervaka anslutningen och aktiviteten för plugin-programmet för Azure Sentinel-utdata aktiverar du lämplig Logstash-loggfil. Se dokumentet [Logstash Directory layout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) för logg filens plats.

Om du inte ser några data i den här logg filen genererar och skickar du vissa händelser lokalt (via plugin-programmet för inkommande och filter) för att se till att utdata-plugin-programmet tar emot data. Azure Sentinel stöder endast problem som rör plugin-programmet för utdata.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Logstash för att ansluta externa data källor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
