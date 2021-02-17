---
title: Migrera Azure Monitor loggar uppdaterings distributioner till Azure Portal
description: Den här artikeln beskriver hur du migrerar Azure Monitor loggar uppdaterings distributioner till Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2e94191e80d39e28d7ff0ffc9aa22b522fda68c1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576028"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Migrera Azure Monitor loggar uppdaterings distributioner till Azure Portal

Hanterings portalen för Operations Management Suite (OMS) är [inaktuell](../azure-monitor/logs/oms-portal-transition.md). Alla funktioner som var tillgängliga i OMS-portalen för Uppdateringshantering finns i Azure Portal, via Azure Monitor loggar. Den här artikeln innehåller den information du behöver för att migrera till Azure Portal.

## <a name="key-information"></a>Viktig information

* Befintliga distributioner fortsätter att fungera. När du har återskapat distributionen i Azure kan du ta bort din gamla distribution.
* Alla befintliga funktioner som du hade i OMS är tillgängliga i Azure. Mer information om Uppdateringshantering finns i [uppdateringshantering översikt](./update-management/overview.md).

## <a name="access-the-azure-portal"></a>Åtkomst till Azure-portalen

1. Från arbets ytan klickar du på **Öppna i Azure**. 

    ![Öppna i Azure – Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. I Azure Portal klickar du på **Automation-konto**

    ![Azure Monitor-loggar](media/migrate-oms-update-deployments/log-analytics.png)

3. Klicka på **uppdateringshantering** i ditt Automation-konto.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Skärm bild av sidan för hantering av uppdateringar.":::

4. I Azure Portal väljer du **Automation-konton** under **alla tjänster**. 

5. Under **hanterings verktyg** väljer du lämpligt Automation-konto och klickar på **uppdateringshantering**.

## <a name="recreate-existing-deployments"></a>Återskapa befintliga distributioner

Alla uppdaterings distributioner som skapas i OMS-portalen har en [Sparad sökning](../azure-monitor/logs/computer-groups.md) som också kallas en dator grupp, med samma namn som den uppdaterings distribution som finns. Den sparade sökningen innehåller en lista över datorer som har schemalagts i uppdaterings distributionen.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Skärm bild av sidan med uppdaterings distributioner med fälten namn och servrar markerade.":::

Följ dessa steg om du vill använda den befintliga sparade sökningen:

1. Om du vill skapa en ny uppdaterings distribution går du till Azure Portal, väljer det Automation-konto som används och klickar på **uppdateringshantering**. Klicka på **Schemalägg uppdaterings distribution**.

    ![Schemalägg uppdaterings distribution](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Fönstret ny uppdaterings distribution öppnas. Ange värden för egenskaperna som beskrivs i följande tabell och klicka sedan på **skapa**:

3. För **datorer som ska uppdateras** väljer du den sparade sökning som används av OMS-distributionen.

    | Egenskap | Beskrivning |
    | --- | --- |
    |Name |Unikt namn som identifierar uppdateringsdistributionen. |
    |Operativsystem| Välj **Linux** eller **Windows**.|
    |Datorer som ska uppdateras |Välj en sparad sökning, importerad grupp eller Välj dator i list rutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/logs/computer-groups.md) |
    |Klassificering av uppdateringar|Välj alla uppdaterings klassificeringar som du behöver. CentOS stöder inte detta.|
    |Uppdateringar som ska undantas|Ange de uppdateringar som ska undantas. För Windows anger du KB-artikeln utan **KB** -prefixet. För Linux anger du paket namnet eller använder ett jokertecken.  |
    |Schemainställningar|Välj tid för start och välj sedan **en gång** eller **återkommande** för upprepningen. | 
    | Underhålls period |Antal minuter som har angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter eller mer än 6 timmar. |
    | Starta om kontroll| Fastställer hur omstarter ska hanteras.</br>De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

4. Klicka på **schemalagda uppdaterings distributioner** om du vill visa statusen för den nyligen skapade uppdaterings distributionen.

    ![ny uppdaterings distribution](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Som tidigare nämnts kan du ta bort befintliga distributioner från Azure Portal när dina nya distributioner har kon figurer ATS via Azure Portal.

## <a name="next-steps"></a>Nästa steg

Mer information om Uppdateringshantering i Azure Automation finns i [uppdateringshantering översikt](./update-management/overview.md).