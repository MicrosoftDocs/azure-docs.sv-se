---
title: Översikt över Azures plattforms loggar | Microsoft Docs
description: Översikt över loggar i Azure Monitor som ger omfattande, frekventa data om driften av en Azure-resurs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: d2162a5c3d88eaae69b3a67456fdc29f19b1a79a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623212"
---
# <a name="overview-of-azure-platform-logs"></a>Översikt över Azure-plattformsloggar
Plattforms loggar ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. De genereras automatiskt även om du behöver konfigurera vissa plattforms loggar som ska vidarebefordras till en eller flera mål som ska behållas. Den här artikeln innehåller en översikt över plattforms loggar, inklusive vilken information de ger och hur du kan konfigurera dem för insamling och analys.

## <a name="types-of-platform-logs"></a>Typer av plattforms loggar
I följande tabell visas de olika plattforms loggar som är tillgängliga på olika lager i Azure.

| Loggas | Skikt | Beskrivning |
|:---|:---|:---|
| [Resursloggar](../platform/resource-logs.md) | Azure-resurser | Ge insikter om åtgärder som utförts i en Azure-resurs ( *data planet*), till exempel för att få en hemlighet från en Key Vault eller göra en begäran till en databas. Innehållet i resurs loggar varierar beroende på Azure-tjänsten och resurs typen.<br><br>*Resurs loggar kallades tidigare för diagnostikloggar.*  |
| [Aktivitetslogg](../essentials/activity-log.md) | Azure Subscription (Azure-prenumeration) | Ger inblick i åtgärderna för varje Azure-resurs i prenumerationen från utsidan (*hanterings planet*) utöver uppdateringar av service Health händelser. Använd aktivitets loggen för att fastställa _vad_, _vem_ och _när_ för Skriv åtgärder (skicka, skicka och ta bort) som ska vidtas för resurserna i din prenumeration. Det finns en enda aktivitets logg för varje Azure-prenumeration. |
| [Azure Active Directory-loggar](../../active-directory/reports-monitoring/overview-reports.md) | Azure-klientorganisation |  Innehåller historiken för inloggnings aktivitet och gransknings spårning av ändringar som gjorts i Azure Active Directory för en viss klient.   |

> [!NOTE]
> Azure aktivitets loggen är främst avsedd för aktiviteter som förekommer i Azure Resource Manager. Den spårar inte resurser med hjälp av klassisk/RDFE-modell. Vissa klassiska resurs typer har en proxy-resurs leverantör i Azure Resource Manager (till exempel Microsoft. ClassicCompute). Om du interagerar med en klassisk resurs typ via Azure Resource Manager med hjälp av dessa providers-proxyservrar, visas åtgärderna i aktivitets loggen. Om du interagerar med en klassisk resurs typ utanför Azure Resource Manager-proxyservrar registreras dina åtgärder bara i åtgärds loggen. Du kan bläddra i åtgärds loggen i ett separat avsnitt i portalen.

![Översikt över plattformsloggar](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visa plattforms loggar
Det finns olika alternativ för att visa och analysera de olika Azure-plattforms loggarna.

- Visa aktivitets loggen i Azure Portal och få åtkomst till händelser från PowerShell och CLI. Mer information finns i [Visa aktivitets loggen](../essentials/activity-log.md#view-the-activity-log) . 
- Visa Azure Active Directory säkerhets-och aktivitets rapporter i Azure Portal. Se [Vad är Azure Active Directory rapporter?](../../active-directory/reports-monitoring/overview-reports.md)  för mer information.
- Resurs loggar skapas automatiskt av Azure-resurser som stöds, men de är inte tillgängliga för visning, om du inte skickar dem till ett [mål](#destinations). 

## <a name="destinations"></a>Mål
Du kan skicka plattforms loggar till en eller flera av målen i följande tabell, beroende på dina övervaknings krav. Konfigurera mål för plattforms loggar genom att [skapa en diagnostisk inställning](../essentials/diagnostic-settings.md).

| Mål | Description |
|:---|:---|
| Log Analytics-arbetsyta | Analysera loggarna för alla dina Azure-resurser och dra nytta av alla funktioner som finns tillgängliga för att [Azure Monitor loggar](../platform/data-platform-logs.md) , inklusive [logg frågor](../log-query/log-query-overview.md) och [logg aviseringar](../alerts/alerts-log.md). Fäst resultatet av en logg fråga på en Azure-instrumentpanel eller ta med den i en arbets bok som en del av en interaktiv rapport. |  |
| Händelsehubb | Skicka plattforms logg data utanför Azure, till exempel en SIEM-plattform från tredje part eller en anpassad telemetri-plattform.
| Azure-lagring | Arkivera loggarna för granskning eller säkerhets kopiering. |

- Mer information om hur du skapar en diagnostisk inställning för aktivitets logg eller resurs loggar finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../essentials/diagnostic-settings.md). 
- Mer information om hur du skapar en diagnostisk inställning för Azure Active Directory loggar finns i följande artiklar.
  - [Integrera Azure AD-loggar med Azure Monitor loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Självstudie: strömma Azure Active Directory loggar till en Azure Event Hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Självstudie: arkivera Azure AD-loggar till ett Azure Storage-konto](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Nästa steg

* [Läs mer information om aktivitets loggen](../essentials/activity-log.md)
* [Läs mer information om resurs loggar](../platform/resource-logs.md)

