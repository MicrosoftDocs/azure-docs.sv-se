---
title: Anslut Azure Activity data till Azure Sentinel | Microsoft Docs
description: Strömma Azure-aktivitets loggar till Azure Sentinel med ett enda klick. I aktivitets loggen registreras och visas händelser på prenumerations nivå i Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595446"
---
# <a name="connect-data-from-azure-activity-log"></a>Anslut data från Azure aktivitets logg

Du kan strömma loggar från [Azure aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md) till Azure Sentinel med ett enda klick. Aktivitets loggen är en prenumerations logg som registrerar och visar händelser på prenumerations nivå i Azure, från Azure Resource Manager operativa data till uppdateringar på Service Health händelser. Med aktivitets loggen kan du bestämma vad, vem och när som ska utföras för alla Skriv åtgärder (skicka, skicka och ta bort) på resurserna i din prenumeration. Du kan också lära dig status för åtgärden och andra relevanta egenskaper. Aktivitets loggen innehåller inte Läs-(GET)-åtgärder eller åtgärder för resurser som använder modellen klassisk/"RDFE". 

## <a name="prerequisites"></a>Förutsättningar

- Användaren måste ha deltagar behörighet till arbets ytan Log Analytics.
- Användaren måste ha läsar behörighet för alla prenumerationer vars loggar du vill strömma till Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Konfigurera Azure Activity Connector

1. Välj **data kopplingar** på navigerings menyn i Azure Sentinel. I listan över anslutningar klickar du på **Azure-aktivitet** och sedan på knappen **Öppna kopplings sidan** längst ned till höger.

2. Klicka på länken **Konfigurera Azure-aktivitets loggar >** under fliken **instruktioner** .

3. I fönstret **Azure aktivitets logg** väljer du de prenumerationer vars loggar du vill strömma till Azure Sentinel. 

4. I rutan prenumeration som öppnas till höger klickar du på **Anslut**.

5. Om du vill använda det relevanta schemat i Log Analytics för Azure-aktivitets aviseringar skriver `AzureActivity` du i frågefönstret.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azures aktivitets logg till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
