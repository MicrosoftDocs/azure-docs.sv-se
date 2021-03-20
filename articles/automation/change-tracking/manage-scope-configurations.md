---
title: Begränsa Azure Automation Ändringsspårning och distributions omfång för inventering
description: Den här artikeln beskriver hur du arbetar med scope-konfigurationer för att begränsa omfånget för en Ändringsspårning och lager distribution.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209993"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Begränsa Ändringsspårning och distributions omfång för inventering

Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder funktionen [ändringsspårning och inventering](overview.md) för att distribuera ändringar i dina virtuella datorer. Mer information finns i [mål övervakning av lösningar i Azure Monitor (för hands version)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Om scope-konfigurationer

En omfattnings konfiguration är en grupp med en eller flera sparade sökningar (frågor) som används för att begränsa omfånget för Ändringsspårning och inventering till vissa datorer. Omfattnings konfigurationen används inom Log Analytics arbets ytan för att rikta in de datorer som ska aktive ras. När du lägger till en dator i ändringarna från funktionen läggs datorn också till i en sparad sökning i arbets ytan.

## <a name="set-the-scope-limit"></a>Ange omfattnings gräns

Begränsa omfånget för din Ändringsspårning-och inventerings distribution:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Automation-konton**.

3. I listan över Automation-konton väljer du det konto som du valde när du aktiverade Ändringsspårning och inventering.

4. I ditt Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

5. Klicka på **gå till arbets yta**.

6. Välj **scope-konfigurationer (för hands version)** under **arbets ytans data källor**.

7. Välj ellipsen till höger om  `MicrosoftDefaultScopeConfig-ChangeTracking` omfattnings konfigurationen och klicka på **Redigera**.

8. I redigerings fönstret väljer du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen. Den sparade sökningen som används av Ändringsspårning och inventering är:

    |Name     |Kategori  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Välj den sparade sökningen för att visa och redigera frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    ![Sparade sökningar](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Ändringsspårning och lager finns i [hantera ändringsspårning och inventering](manage-change-tracking.md).
* Information om hur du felsöker allmänna funktions problem finns i [felsöka ändringsspårning-och inventerings problem](../troubleshoot/change-tracking.md).
