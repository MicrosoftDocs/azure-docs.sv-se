---
title: Felsöka SQL Insights (för hands version)
description: Felsöka SQL Insights i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610110"
---
# <a name="troubleshooting-sql-insights-preview"></a>Felsöka SQL Insights (för hands version)
Om du vill felsöka problem med data insamling i SQL Insights kontrollerar du status för övervaknings datorn på fliken **Hantera profil** . Detta kommer att ha något av följande tillstånd:

- Kräva 
- Samlar inte in 
- Samlar in med fel 
 
Klicka på **status** för att gå vidare till Visa loggar och ytterligare information som kan hjälpa dig att lösa problemet. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Övervaknings dator status":::

## <a name="not-collecting-state"></a>Samlar inte in tillstånd 
Övervaknings datorn har statusen *inte samla in* om det inte finns några data i *InsightsMetrics* för SQL under de senaste 10 minuterna. 

SQL Insights använder följande fråga för att hämta den här informationen:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Kontrol lera om det finns några loggar från teleympkvistar som hjälper till att identifiera den bakomliggande orsaken till problemet. Om det finns logg poster kan du klicka på *samla inte in* och kontrol lera loggarna och fel söknings informationen för vanliga problem. 


Om det inte finns några loggar måste du kontrol lera loggarna på den virtuella datorn för övervakning för följande tjänster som installerats av två tillägg för virtuell dator:

- Microsoft. Azure. Monitor. AzureMonitorLinuxAgent 
  - Tjänst: mdsd 
- Microsoft. Azure. Monitor. arbets belastningar. WLILinuxExtension för arbets belastning. 
  - Tjänst: WLI 
  - Tjänst: MS-teleympkvistar 
  - Tjänst: TD-agent-bit-WLI 
  - Tilläggs logg för att kontrol lera installations felen:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>WLI tjänst loggar 

Tjänst loggar: `/var/log/wli.log`

Så här visar du de senaste loggarna: `tail -n 100 -f /var/log/wli.log`
 

Om du ser följande fel logg visas ett problem med **mdsd** -tjänsten.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Loggar för teleympkvistar-tjänsten 

Tjänst loggar: `/var/log/ms-telegraf/telegraf.log`

Så här visar du de senaste loggarna: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` om du vill visa senaste fel och varnings loggar: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Konfigurationen som används av teleympkvistar genereras av WLI-tjänsten och placeras i: `/etc/ms-telegraf/telegraf.d/wli`
 
Om en felaktig konfiguration genereras MS-teleympkvistar-tjänsten kanske inte startar, kontrol lera om MS-teleympkvistar-tjänsten körs med kommandot: `service ms-telegraf status`

Om du vill visa fel meddelanden från tjänsten teleympkvistar kör du det manuellt med följande kommando: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd tjänst loggar 

Kontrol lera [aktuella begränsningar](../agents/azure-monitor-agent-overview.md#current-limitations) för Azure Monitor agenten. 


Tjänst loggar:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Så här visar du de senaste felen: `tail -n 100 -f /var/log/mdsd.err`

 Om du behöver kontakta supporten samlar du in följande information: 

- Loggar in `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Logga in `/var/log/waagent.log` 
- Loggar in `/var/log/mdsd*`
- Filer i `/etc/mdsd.d/`
- Arkiv `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Ogiltig konfiguration av virtuell dator för övervakning

En orsak till att *inte samla in* är när du har en ogiltig övervakning av konfigurationen för virtuella datorer.  Följande är standard konfigurationen:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Den här konfigurationen anger de ersättnings-token som ska användas i profil konfigurationen på den virtuella övervaknings datorn. Du kan också referera till hemligheter från Azure Key Vault, så du behöver inte ha några hemliga värden i någon konfiguration, vilket är starkt rekommenderat.

#### <a name="secrets"></a>Hemligheter
Hemligheter är tokens vars värden hämtas vid körning från en Azure Key Vault. En hemlighet definieras av ett par med en Key Vault referens och ett hemligt namn. Detta gör att Azure Monitor kan hämta det dynamiska värdet för hemligheten och använda det är underordnade konfigurations referenser.

Du kan ange så många hemligheter som behövs i konfigurationen, inklusive hemligheter lagrade i separata nyckel valv.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Behörigheterna för att komma åt Key Vault ges till en Hanterad tjänstidentitet på den virtuella övervaknings datorn. Azure Monitor förväntar sig att Key Vault tillhandahålla minst hemligheter få behörighet till den virtuella datorn. Du kan aktivera den från Azure Portal-, PowerShell-, CLI-eller Resource Manager-mallen.

#### <a name="parameters"></a>Parametrar
Parametrar är tokens som kan refereras i profil konfigurationen via JSON-mall. Parametrarna har ett namn och ett värde. Värden kan vara vilken JSON-typ som helst, inklusive objekt och matriser. En parameter refereras till i profil konfigurationen med hjälp av namnet i den här konventionen `.Parameters.<name>` .

Parametrar kan referera till hemligheter i Key Vault att använda samma konvention. `sqlAzureConnections`Refererar exempelvis till hemligheten `telegrafPassword` med hjälp av konventionen `$telegrafPassword` .

Vid körning kommer alla parametrar och hemligheter att lösas och sammanfogas med profil konfigurationen för att skapa den faktiska konfigurationen som ska användas på datorn.

> [!NOTE]
> Parameter namnen för `sqlAzureConnections` , `sqlVmConnections` och `sqlManagedInstanceConnections` är alla obligatoriska i konfigurationen även om du inte har anslutnings strängar som du kommer att tillhandahålla för några av dem.


## <a name="collecting-with-errors-state"></a>Samlar in med fel tillstånd
Övervaknings datorn befinner sig i tillstånds *insamling med fel* om det finns minst en *InsightsMetrics* -logg men det finns även fel i *Åtgärds* tabellen.

SQL Insights använder följande frågor för att hämta den här informationen:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

För vanliga fall ger vi fel söknings kunskap i vår loggfiler: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vyn fel söknings loggar.":::



## <a name="next-steps"></a>Nästa steg

- Få information om hur du [aktiverar SQL Insights](sql-insights-enable.md).
