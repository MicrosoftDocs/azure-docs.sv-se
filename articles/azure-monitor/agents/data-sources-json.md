---
title: Samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor
description: Anpassade JSON-datakällor kan samlas in i Azure Monitor med hjälp av Log Analytics-agenten för Linux.  De här anpassade data källorna kan vara enkla skript som returnerar JSON, till exempel sväng eller ett av de populäraste, 300 + plugin-programmen. I den här artikeln beskrivs den konfiguration som krävs för den här data insamlingen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: fe80a5c117e8c94e5df946813a1c025747ff40e8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050706"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Anpassade JSON-datakällor kan samlas in i [Azure Monitor](../data-platform.md) med hjälp av Log Analytics-agenten för Linux.  De här anpassade data källorna kan vara enkla skript som returnerar JSON, till exempel [sväng](https://curl.haxx.se/) eller ett av de populäraste, [300 + plugin](https://www.fluentd.org/plugins/all)-programmen. I den här artikeln beskrivs den konfiguration som krävs för den här data insamlingen.


> [!NOTE]
> Log Analytics agent för Linux v 1.1.0-217 + krävs för anpassade JSON-data

## <a name="configuration"></a>Konfiguration

### <a name="configure-input-plugin"></a>Konfigurera plugin-programmet för indatamängd

Om du vill samla in JSON-data i Azure Monitor lägger du till i `oms.api.` början av en Fluent-tagg i ett plugin-program för indata.

Följande är till exempel en separat konfigurations fil `exec-json.conf` i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  Detta använder det Fluent-plugin-programmet `exec` för att köra ett spiral kommando var 30: e sekund.  Utdata från det här kommandot samlas in av JSON-utdata-plugin-programmet.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

Den konfigurations fil som läggs till i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` kräver att dess ägarskap ändras med följande kommando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurera plugin-program för utdata 
Lägg till följande plugin-konfiguration för utdata i huvud konfigurationen i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` eller som en separat konfigurations fil som placerats i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Starta om Log Analytics agent för Linux
Starta om Log Analytics agent för Linux-tjänsten med följande kommando.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Utdata
Data samlas in i Azure Monitor med post typen `<FLUENTD_TAG>_CL` .

Till exempel den anpassade taggen `tag oms.api.tomcat` i Azure monitor med post typen `tomcat_CL` .  Du kan hämta alla poster av den här typen med följande logg fråga.

```console
Type=tomcat_CL
```

Kapslade JSON-datakällor stöds, men indexeras baserat på överordnat fält. Till exempel returneras följande JSON-data från en logg fråga som `tag_s : "[{ "a":"1", "b":"2" }]` .

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.