---
title: Samla in data från insamlade Azure Monitor | Microsoft Docs
description: Insamlad är en Linux-daemon med öppen källkod som regelbundet samlar in data från program och system nivå information.  Den här artikeln innehåller information om att samla in data från insamlade Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: a0efeaa3df0ecc69fa29dcb2cbb50874c4ab486a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623464"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Samla in data från insamlade på Linux-agenter i Azure Monitor
[Insamlad](https://collectd.org/) är en Linux-daemon med öppen källkod som regelbundet samlar in prestanda mått från program och information på system nivå. Exempel på program är Java Virtual Machine (JVM), MySQL server och Nginx. Den här artikeln innehåller information om hur du samlar in prestanda data från insamlade Azure Monitor.

En fullständig lista över tillgängliga plugin-program finns i [tabellen med plugin](https://collectd.org/wiki/index.php/Table_of_Plugins)-program.

![Insamlad översikt](media/data-sources-collectd/overview.png)

Följande insamlade konfiguration ingår i Log Analytics agent för Linux för att dirigera insamlade data till Log Analytics agenten för Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Om du använder en version som samlats in före 5,5 använder du dessutom följande konfiguration i stället.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

Den insamlade konfigurationen använder standard- `write_http` plugin-programmet för att skicka prestanda måtts data via port 26000 till Log Analytics-agenten för Linux. 

> [!NOTE]
> Den här porten kan konfigureras till en anpassad port vid behov.

Log Analytics agenten för Linux lyssnar också på port 26000 för insamlade mått och konverterar dem sedan till Azure Monitor schema mått. Följande är Log Analytics agent för Linux-konfiguration  `collectd.conf` .

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> Som standard är inställt på Läs värden med 10 sekunders [intervall](https://collectd.org/wiki/index.php/Interval). Eftersom detta direkt påverkar mängden data som skickas till Azure Monitor loggar kan du behöva justera intervallet inom den insamlade konfigurationen för att skapa en fin balans mellan övervaknings kraven och tillhör ande kostnader och användning för Azure Monitors loggar.

## <a name="versions-supported"></a>Versioner som stöds
- Azure Monitor stöder för närvarande samlad version 4,8 och senare.
- Log Analytics agent för Linux v 1.1.0-217 eller senare krävs för insamling av insamlade mått.


## <a name="configuration"></a>Konfiguration
Följande är grundläggande steg för att konfigurera insamling av insamlade data i Azure Monitor.

1. Konfigurera insamlad för att skicka data till Log Analytics agent för Linux med hjälp av write_http plugin-programmet.  
2. Konfigurera Log Analytics agent för Linux för att lyssna efter insamlade data på lämplig port.
3. Starta om samlad och Log Analytics agent för Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurera insamlad för att vidarebefordra data 

1. För att dirigera insamlade data till Log Analytics agenten för Linux `oms.conf` måste läggas till i den insamlade konfigurations katalogen. Filens mål är beroende av datorns Linux-distribution.

    Om din uppsamlade konfigurations katalog finns i/etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Om din uppsamlade konfigurations katalog finns i/etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >För insamlade versioner före 5,5 måste du ändra taggarna i `oms.conf` som visas ovan.
    >

2. Kopiera insamlad. conf till den önskade arbets ytans omsagent-konfigurations katalog.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Starta om insamlad och Log Analytics agent för Linux med följande kommandon.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Insamlade mått till Azure Monitor schema konvertering
För att upprätthålla en välbekant modell mellan infrastruktur mått som redan har samlats in av Log Analytics agent för Linux och de nya mått som samlas in genom att samla in följande schema mappning används:

| Fältet insamlat mått | Azure Monitor fält |
|:--|:--|
| `host` | Dator |
| `plugin` | Inget |
| `plugin_instance` | Instans namn<br>Om **plugin_instance** är *Null* then = "*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Om **type_instance** är *Null* är CounterName =**tomt** |
| `dsnames[]` | CounterName |
| `dstypes` | Inget |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar. 
* Använd [anpassade fält](../platform/custom-fields.md) för att parsa data från syslog-poster i enskilda fält.
