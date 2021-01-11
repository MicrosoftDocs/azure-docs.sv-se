---
title: Azure Monitor fel söknings loggar (för hands version)
description: Använd Azure Monitor för att snabbt eller regelbundet undersöka problem, felsöka kod-eller konfigurations problem eller support ärenden, som ofta förlitar sig på att söka i stora data mängder för specifika insikter.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067373"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor fel söknings loggar (för hands version)
Använd Azure Monitor för att snabbt och/eller regelbundet undersöka problem, felsöka kod-eller konfigurations problem eller adress support ärenden, som ofta förlitar sig på att söka i stora data mängder för specifika insikter.

>[!NOTE]
> * Fel söknings loggar är i förhands gransknings läge.
>* Kontakta [Log Analytics-teamet](mailto:orens@microsoft.com) med frågor eller Använd funktionen.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Felsök och fråga efter problem med din kod eller konfiguration
Använd Azure Monitor fel söknings loggar för att hämta dina poster och undersöka problem och problem på ett enklare och billigare sätt med hjälp av KQL.
Fel söknings loggar i dekret om dina avgifter genom att ge dig grundläggande funktioner för fel sökning.

> [!NOTE]
>* Beslutet om fel söknings läge kan konfigureras.
>* Fel söknings loggar kan tillämpas på vissa tabeller, för närvarande i tabellerna "behållar loggar" och "app traces".
>* Det finns en kostnads fri kvarhållningsperiod på 4 dagar, som kan utökas i tillägg till kostnader.
>* Som standard ärver tabellerna arbets ytans kvarhållning. För att undvika ytterligare avgifter rekommenderar vi att du ändrar dessa tabeller kvarhållning. [Klicka här om du vill lära dig att ändra tabell kvarhållning](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Aktivera fel söknings loggar i dina tabeller

Om du vill aktivera fel söknings loggar i din arbets yta måste du använda följande API-anrop.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Kontrol lera om funktionen fel söknings loggar är aktive rad för en specifik tabell
Du kan kontrol lera om fel söknings loggen är aktive rad för en specifik tabell genom att använda följande API-anrop.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Kontrol lera om funktionen fel söknings loggar är aktive rad för alla tabeller i en arbets yta
Du kan använda följande API-anrop för att kontrol lera vilka tabeller där fel söknings loggen är aktive rad.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Inaktivera fel söknings loggar i dina tabeller

Om du vill inaktivera fel söknings loggar i din arbets yta måste du använda följande API-anrop.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* Du kan använda valfritt REST API-verktyg för att köra kommandona. [Läs mer](https://docs.microsoft.com/rest/api/azure/)
>* Du måste använda Bearer-token för autentisering. [Läs mer](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* Flaggan "isTroubleshootingAllowed" – beskriver om tabellen tillåts i tjänsten
>* "IsTroubleshootEnabled" anger om funktionen är aktive rad för tabellen – kan växlas eller inaktive ras (sant eller falskt)
>* När du inaktiverar flaggan "isTroubleshootEnabled" för en enskild tabell, så är det möjligt att återaktivera det bara en vecka efter föregående aktiverings datum.
>* För närvarande stöds detta endast för tabeller under (vissa andra SKU: er kommer också att stödjas i framtiden) – [Läs mer om prissättning](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Frågor om begränsningar för fel sökning
Det finns några begränsningar för en tabell som har marker ATS som "fel söknings loggar":
*   Kommer att få mindre bearbetnings resurser och är därför inte lämplig för stora instrument paneler, komplex analys eller många samtidiga API-anrop.
*   Frågor är begränsade till ett tidsintervall på två dagar.
* tömningen fungerar inte – [Läs mer om att rensa](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* Aviseringar stöds inte via den här tjänsten.
## <a name="next-steps"></a>Nästa steg
* [Skriva frågor](https://docs.microsoft.com/azure/data-explorer/write-queries)


