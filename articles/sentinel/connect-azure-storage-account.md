---
title: Ansluta Azure Storage-kontodiagnostikloggar till Azure Sentinel
description: Lär dig hur du använder Azure Policy för att ansluta Azure Storage-kontodiagnostikloggar till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879137"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Ansluta Azure Storage diagnostikloggar för konto

Azure Storage är en molnlösning för moderna datalagringsscenarier. Den innehåller alla dina dataobjekt: blobar, filer, köer, tabeller och diskar.

Med den här anslutningsappen kan du strömma dina Azure Storage-kontons diagnostikloggar till Azure Sentinel, så att du kontinuerligt kan övervaka aktiviteter och identifiera säkerhetshot i alla dina Azure Storage-resurser i hela organisationen.

Läs mer om [att övervaka Azure Storage konto](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Förutsättningar

Mata in Azure Storage-kontodiagnostikloggar i Azure Sentinel:

- Du måste ha läs- och skrivbehörighet på Azure Sentinel arbetsytan.

- Om du Azure Policy använda en loggströmningsprincip för Azure Storage resurser måste du ha rollen Ägare för principtilldelningsomfånget.

## <a name="connect-to-azure-storage-account"></a>Ansluta till Azure Storage konto

Den här anslutningsappen Azure Policy för att tillämpa en enda loggströmningskonfiguration på en samling Azure Storage-kontoresurser, definierade som ett omfång. Du kan se tillgängliga Azure Storage och måtttyper till vänster på anslutningssidan under **Datatyper**.

1. På Azure Sentinel väljer du **Dataanslutningsappar.**

1. Välj **Azure Storage konto** i galleriet med dataanslutningsappar och välj sedan Öppna **anslutningssidan** i förhandsgranskningsfönstret.

1. I avsnittet **Konfiguration på** anslutningssidan expanderar du **Stream-diagnostikloggar från ditt Azure Storage konto i stor skala.**

1. Välj knappen **Starta Azure Policy Tilldelning.**

    Guiden principtilldelning öppnas och du kan skapa en ny princip med namnet **Konfigurera diagnostikinställningar för lagringskonton till Log Analytics-arbetsytan.**

    1. På fliken **Grundläggande klickar** du på knappen med  de tre punkterna bredvid Omfång för att välja din prenumeration (och eventuellt en resursgrupp). Du kan också lägga till en beskrivning.

    1. På **fliken** Parametrar:
        - Välj Azure Sentinel arbetsyta i **listrutan Log** Analytics-arbetsyta.
        - Välj de **lagringsresurstyper** (fil, tabell, kö osv.) som du vill distribuera diagnostikinställningar till i listrutan Lagringstjänster för distribution.
        - Lämna fälten **Inställningsnamn** **och** Effekt som de är.
        - De återstående listrutan visar tillgängliga diagnostikloggar och måtttyper. Lämna markerat som "True" (Sant) för alla typer som du vill mata in.

    1. Stegen ovan tillämpar principen på alla framtida lagringsresurser. Om du vill tillämpa principen på dina befintliga resurser **väljer du fliken** Reparation och markerar kryssrutan Skapa en **åtgärdsaktivitet.**

    1. På fliken **Granska + skapa** klickar du på **Skapa**. Principen har nu tilldelats till det omfång som du har valt.

> [!NOTE]
>
> Med den här specifika dataanslutningen visas anslutningsstatusindikatorerna (en färgstrimpa i galleriet med dataanslutningsappar och anslutningsikoner bredvid datatypnamnen) som anslutna *(gröna)* endast om data har matats in någon gång under de senaste 14 dagarna. När 14 dagar har passerat utan datainmatning visas anslutningsappen som frånkopplad. Så fort mer data kommer igenom *returneras den* anslutna statusen.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Storage-konto till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur [du får insyn i dina data och potentiella hot.](quickstart-get-visibility.md)
- Kom igång [med att identifiera hot Azure Sentinel](tutorial-detect-threats-built-in.md).
