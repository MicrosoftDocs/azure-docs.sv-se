---
title: Samla in & analysera resurs loggar
description: Registrera och analysera resurs logg händelser för Azure Container Registry, till exempel autentisering, avbildnings-push och image pull.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572005"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry loggar för diagnostisk utvärdering och granskning

Den här artikeln beskriver hur du samlar in loggdata för ett Azure Container Registry med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [resurs loggar](../azure-monitor/essentials/platform-logs-overview.md) (tidigare kallade *diagnostikloggar*) för användar drivna händelser i registret. Samla in och använda dessa data för att möta behoven, till exempel:

* Granska händelser för autentisering av registret för att säkerställa säkerhet och efterlevnad 

* Tillhandahålla en fullständig aktivitets spårning på register artefakter som pull-och pull-händelser så att du kan diagnostisera drift problem med ditt register 

Att samla in resurs logg data med Azure Monitor kan medföra ytterligare kostnader. Se [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Databas händelser

Följande händelser på lagrings nivå för bilder och andra artefakter är för närvarande loggade:

* **Push**
* **Listruta**
* **Avtagga**
* **Ta bort** (inklusive databas borttagnings händelser)
* **Rensa tagg** och **Rensa manifest**

> [!NOTE]
> Rensnings händelser loggas bara om en [princip](container-registry-retention-policy.md) för register lagring har kon figurer ATS.

## <a name="registry-resource-logs"></a>Register resurs loggar

Resurs loggar innehåller information som genereras av Azure-resurser som beskriver den interna åtgärden. I ett Azure Container Registry innehåller loggarna autentiserings-och lagrings nivå händelser som lagras i följande tabeller. 

* **ContainerRegistryLoginEvents**  – händelser och status för klientautentisering, inklusive inkommande identitet och IP-adress
* **ContainerRegistryRepositoryEvents** – åtgärder som push och pull för avbildningar och andra artefakter i register databaser
* **AzureMetrics**  -  [Container Registry-mått](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) som aggregerade push-och pull-antal.

För åtgärder innehåller loggdata följande:
  * Status för lyckades eller misslyckades
  * Start-och slutdatum stämplar

Förutom resurs loggar tillhandahåller Azure en [aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md), en enda post på prenumerations nivå med Azures hanterings händelser, till exempel när ett behållar register skapas eller tas bort.

## <a name="enable-collection-of-resource-logs"></a>Aktivera insamling av resurs loggar

Insamling av resurs loggar för ett behållar register är inte aktiverat som standard. Aktivera diagnostiska inställningar uttryckligen för varje register som du vill övervaka. Alternativ för att aktivera diagnostikinställningar finns i [skapa diagnostisk inställning för att samla in plattforms loggar och mått i Azure](../azure-monitor/essentials/diagnostic-settings.md).

Om du till exempel vill visa loggar och mått för ett behållar register i nära real tid i Azure Monitor, samla in resurs loggarna i en Log Analytics arbets yta. Om du vill aktivera den här diagnostikinställningar använder du Azure Portal:

1. Om du inte redan har en arbets yta skapar du en arbets yta med hjälp av [Azure Portal](../azure-monitor/logs/quick-create-workspace.md). För att minimera svars tiden i data insamlingen kontrollerar du att arbets ytan finns i **samma region** som behållar registret.
1. Välj registret i portalen och välj **övervakning > diagnostikinställningar > Lägg till diagnostisk inställning**.
1. Ange ett namn för inställningen och välj **Skicka till Log Analytics**.
1. Välj arbets ytan för registrets diagnostikloggar.
1. Välj de logg data som du vill samla in och klicka på **Spara**.

Följande bild visar hur du skapar en diagnostisk inställning för ett register med hjälp av portalen.

![Aktivera diagnostikinställningar](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Samla bara in de data som du behöver, balansera kostnaden och dina övervaknings behov. Om du till exempel bara behöver granska autentiseringsbegäranden väljer du bara loggen **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Visa data i Azure Monitor

När du har aktiverat insamling av diagnostikloggar i Log Analytics kan det ta några minuter innan data visas i Azure Monitor. Om du vill visa data i portalen väljer du registret och väljer **övervaka > loggar**. Välj en av tabellerna som innehåller data för registret. 

Kör frågor för att visa data. Det finns flera exempel frågor eller så kör du egna. Följande fråga hämtar till exempel de senaste 24 timmarna av data från tabellen **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

Följande bild visar exempel på utdata:

![Frågeloggdata](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

En själv studie kurs om hur du använder Log Analytics i Azure Portal finns i [Kom igång med Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)eller testa Log Analytics [demo miljö](https://portal.loganalytics.io/demo). 

Mer information om logg frågor finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="query-examples"></a>Exempelfrågor

### <a name="error-events-from-the-last-hour"></a>Fel händelser från den senaste timmen

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 senaste register händelser

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identitet för användaren eller objektet som tog bort databasen

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identitet för användaren eller objektet som tog bort taggen

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Åtgärds problem på databas nivå

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Autentiseringsfel för registret

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Ytterligare logg destinationer

Förutom att skicka loggarna till Log Analytics, eller som ett alternativ, är ett vanligt scenario att välja ett Azure Storage konto som ett logg mål. Om du vill arkivera loggar i Azure Storage skapar du ett lagrings konto innan du aktiverar arkivering genom diagnostiska inställningar.

Du kan också strömma diagnostikloggar till en [Azure Event Hub](../event-hubs/event-hubs-about.md). Event Hubs kan mata in miljon tals händelser per sekund, som du sedan kan omvandla och lagra med valfri analys-Provider i real tid. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att använda [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) och skapa [logg frågor](../azure-monitor/logs/get-started-queries.md).
* Se [Översikt över Azures plattforms loggar](../azure-monitor/essentials/platform-logs-overview.md) för att lära dig om plattforms loggar som är tillgängliga på olika lager i Azure.