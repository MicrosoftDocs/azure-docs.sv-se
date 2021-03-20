---
title: Begränsa Azure Automation Uppdateringshantering distributions omfång
description: Den här artikeln beskriver hur du använder scope-konfigurationer för att begränsa omfånget för en Uppdateringshantering-distribution.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222534"
---
# <a name="limit-update-management-deployment-scope"></a>Begränsa Uppdateringshantering distributions omfång

Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder funktionen [uppdateringshantering](overview.md) för att distribuera uppdateringar och korrigeringar till dina virtuella datorer. Mer information finns i [mål övervakning av lösningar i Azure Monitor (för hands version)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Om scope-konfigurationer

En omfattnings konfiguration är en grupp med en eller flera sparade sökningar (frågor) som används för att begränsa omfånget för Uppdateringshantering till vissa datorer. Omfattnings konfigurationen används inom Log Analytics arbets ytan för att rikta in de datorer som ska aktive ras. När du lägger till en dator för att ta emot uppdateringar från Uppdateringshantering läggs datorn också till i en sparad sökning i arbets ytan.

## <a name="set-the-scope-limit"></a>Ange omfattnings gräns

Begränsa omfånget för din Uppdateringshantering-distribution:

1. I ditt Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

2. Välj **gå till arbets yta**.

3. Välj **scope-konfigurationer (för hands version)** under **arbets ytans data källor**.

4. Välj ellipsen till höger om  `MicrosoftDefaultScopeConfig-Updates` omfattnings konfigurationen och välj **Redigera**.

5. I redigerings fönstret expanderar du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen. Den sparade sökningen som används av Uppdateringshantering är:

    |Name     |Kategori  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

6. Välj den sparade sökningen för att visa och redigera frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    [![Sparade sökningar](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Du kan [fråga Azure Monitor loggar](query-logs.md) för att analysera uppdaterings utvärderingar, distributioner och andra relaterade hanterings uppgifter. Den innehåller fördefinierade frågor som hjälper dig att komma igång.
