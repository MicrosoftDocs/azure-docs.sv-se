---
title: Konfigurera loggning för att övervaka Logic Apps i Azure Security Center
description: Övervaka hälsan för dina Logic Apps resurser i Azure Security Center genom att konfigurera diagnostisk loggning.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712393"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Konfigurera loggning för att övervaka Logic Apps i Azure Security Center

När du övervakar Logic Apps resurser i [Microsoft Azure Security Center](../security-center/security-center-introduction.md)kan du [se om dina Logi Kap par följer standard principerna](#view-logic-apps-health-status). Azure visar hälso status för en Logic Apps resurs när du har aktiverat loggning och konfigurerat loggarna korrekt. Den här artikeln förklarar hur du konfigurerar diagnostikloggning och ser till att alla dina Logi Kap par är felfria resurser.

> [!TIP]
> Om du vill hitta aktuell status för Logic Apps tjänsten granskar du [sidan Azure-status](https://status.azure.com/), som visar statusen för olika produkter och tjänster i varje tillgänglig region.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Befintliga Logi Kap par med [diagnostisk loggning aktive rad](#enable-diagnostic-logging).
* En Log Analytics arbets yta, vilket krävs för att aktivera loggning för din Logic app. Om du inte har en arbets yta måste du först [skapa din arbets yta](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Aktivera diagnostisk loggning

Innan du kan visa resurs hälso status för dina Logi Kap par måste du först [Konfigurera diagnostikloggning](monitor-logic-apps-log-analytics.md). Om du redan har en Log Analytics arbets yta kan du aktivera loggning antingen när du skapar din Logi Kap par eller befintliga Logic Apps.

> [!TIP]
> Standard rekommendationen är att aktivera diagnostikloggar för Logic Apps. Du styr dock den här inställningen för dina Logic Apps. När du aktiverar diagnostikloggar för dina Logic Apps kan du använda informationen för att analysera säkerhets incidenter.

### <a name="check-diagnostic-logging-setting"></a>Kontrol lera inställningen diagnostisk loggning

Om du inte är säker på om dina Logi Kap par har aktive rad diagnostisk loggning kan du checka in Security Center:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Skriv i Sök fältet och välj **Security Center**.
1. På menyn Security Center instrument panel väljer du **rekommendationer** under **Allmänt**.
1. I tabellen med säkerhets förslag kan du söka efter och välja **Aktivera gransknings-och loggnings** &gt; **diagnostikloggar i Logic Apps ska aktive ras** i tabellen med säkerhets kontroller.
1. På sidan rekommendation expanderar du avsnittet om **reparations steg** och granskar alternativen. Du kan aktivera Logic Apps diagnostik genom att välja **snabb korrigering!** eller genom att följa anvisningarna för manuella åtgärder.

## <a name="view-logic-apps-health-status"></a>Visa hälso status för Logic Apps

När du har [aktiverat diagnostisk loggning](#enable-diagnostic-logging)kan du se hälso status för dina Logi Kap par i Security Center.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Skriv i Sök fältet och välj **Security Center**.
1. På menyn Security Center instrument panel väljer du **inventering** under **Allmänt**.
1. På sidan inventering filtrerar du till gångar-listan så att endast Logic Apps resurser visas. I menyn sida väljer du **resurs typer** &gt; **Logic Apps**.

   Räknaren för **felaktiga resurser** visar antalet Logi Kap par som Security Center anser att de inte är felfria.
1.  Granska **rekommendationer** -kolumnen i listan över resurser för Logic Apps. Om du vill granska hälso informationen för en speciell Logic-app väljer du ett resurs namn eller så klickar du på knappen ovaler (**...**) &gt; **Visa resurs**.
1.  Följ stegen som visas för dina Logi Kap par för att åtgärda eventuella problem med resurs hälsa.

Om diagnostikloggar redan är aktive rad kan det finnas ett problem med målet för dina loggar. Läs [om hur du åtgärdar problem med olika loggnings destinationer för diagnostik](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Korrigera diagnostisk loggning för Logic Apps

Om dina Logi Kap par [visas som skadade i Security Center](#view-logic-apps-health-status)öppnar du din Logic app i kodvyn i Azure Portal eller via Azure CLI. Kontrol lera sedan mål konfigurationen för dina diagnostikloggar: [azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)eller [ett Azure Storage-konto](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics och Event Hubs destinationer

Om du använder Log Analytics eller Event Hubs som mål för Logic Apps diagnostikloggar kontrollerar du följande inställningar. 

1. Kontrol lera att du har aktiverat diagnostikloggar genom att kontrol lera att `logs.enabled` fältet diagnostikinställningar är inställt på `true` . 
1. För att bekräfta att du inte har angett ett lagrings konto som mål i stället, kontrollerar du att `storageAccountId` fältet är inställt på `false` .

Exempel:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Mål för lagrings konto

Om du använder ett lagrings konto som mål för Logic Apps diagnostikloggar kontrollerar du följande inställningar.

1. Kontrol lera att du har aktiverat diagnostikloggar genom att kontrol lera att `logs.enabled` fältet diagnostikinställningar är inställt på `true` .
1. Kontrol lera att du har aktiverat en bevarande princip för dina diagnostikloggar genom att kontrol lera att `retentionPolicy.enabled` fältet är inställt på `true` .
1. För att bekräfta att du ställer in en kvarhållningsperiod på 0-365 dagar, kontrollerar du `retentionPolicy.days` att fältet är inställt på ett tal mellan 0 och 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```