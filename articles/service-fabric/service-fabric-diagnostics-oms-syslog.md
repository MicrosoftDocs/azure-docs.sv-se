---
title: Övervaka Linux-kluster händelser i Azure Service Fabric
description: Lär dig hur du övervakar ett Service Fabric Linux-kluster händelser genom att skriva Service Fabric plattforms händelser till syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 6a98a833baefa9b1c2d1ebd9ff8147206c2106bf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570195"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Service Fabric Linux-kluster händelser i syslog

Service Fabric visar en uppsättning plattforms händelser som informerar dig om viktig aktivitet i klustret. En fullständig lista över händelser som visas finns [här](service-fabric-diagnostics-event-generation-operational.md). Det finns flera olika sätt att utnyttja dessa händelser. I den här artikeln ska vi diskutera hur du konfigurerar Service Fabric att skriva händelserna till syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introduktion

I 6,4-versionen har SyslogConsumer lanserats för att skicka Service Fabric plattforms händelser till syslog för Linux-kluster. När händelsen har Aktiver ATS flödar händelser automatiskt till syslog, som kan samlas in och skickas av Log Analytics agenten.

Varje syslog-händelse har 4 komponenter
* Anläggning
* Identitet
* Meddelande
* Allvarlighetsgrad

SyslogConsumer skriver alla plattforms händelser med hjälp av funktionen `Local0` . Du kan uppdatera till en giltig funktion genom att ändra konfigurations konfigurationen. Den identitet som används är `ServiceFabric` . Meddelande fältet innehåller hela händelsen serialiserad i JSON, så att den kan frågas och konsumeras av en rad olika verktyg. 

## <a name="enable-syslogconsumer"></a>Aktivera SyslogConsumer

Om du vill aktivera SyslogConsumer måste du utföra en uppgradering av klustret. `fabricSettings`Avsnittet måste uppdateras med följande kod. OBS! den här koden innehåller bara avsnitt som rör SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Här är ändringarna för att anropa
1. I avsnittet common finns en ny parameter som heter `LinuxStructuredTracesEnabled` . **Detta krävs för att Linux-händelser ska struktureras och serialiseras när de skickas till syslog.**
2. I avsnittet diagnostik har en ny ConsumerInstance: SyslogConsumer lagts till. Detta talar om för plattformen att det finns en annan konsument av händelserna. 
3. Det nya avsnittet SyslogConsumer måste ha `IsEnabled` as `true` . Den konfigureras att använda Local0-funktionen automatiskt. Du kan åsidosätta detta genom att lägga till en annan parameter.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure Monitor loggar integrering
Du kan läsa dessa Syslog-händelser i ett övervaknings verktyg, till exempel Azure Monitor loggar. Du kan skapa en Log Analytics-arbetsyta med hjälp av Azure Marketplace med hjälp av dessa [instruktioner]. (.. /Azure-Monitor/logs/Quick-Create-Workspace.MD) du måste också lägga till Log Analytics agenten till klustret för att samla in och skicka dessa data till arbets ytan. Det här är samma agent som används för att samla in prestanda räknare. 

1. Navigera till `Advanced Settings` bladet

    ![Inställningar för arbets yta](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klicka på `Data`
3. Klicka på `Syslog`
4. Konfigurera Local0 som den funktion som ska spåras. Du kan lägga till en annan funktion om du har ändrat den i fabricSettings

    ![Konfigurera syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Gå till Query Explorer genom att klicka på `Logs` arbets ytans resurs meny för att starta frågan

    ![Arbets ytans loggar](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Du kan fråga mot `Syslog` tabellen som söker efter `ServiceFabric` processname. Frågan nedan är ett exempel på hur du kan parsa JSON i händelsen och visa dess innehåll

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog-fråga](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Exemplet ovan är en NodeDown-händelse. Du kan visa en fullständig lista över händelser [här](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics agenten](service-fabric-diagnostics-oms-agent.md) på noderna för att samla in prestanda räknare och samla Docker statistik och loggar för dina behållare
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar
* [Använd Visa designer för att skapa anpassade vyer i Azure Monitor loggar](../azure-monitor/visualize/view-designer.md)
* Referens för hur du [Azure Monitor loggar integrering med syslog](../azure-monitor/agents/data-sources-syslog.md).
