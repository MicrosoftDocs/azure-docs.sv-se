---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83343362"
---
Använd följande procedur för att visa och uppdatera den körningsversion som för närvarande används av en funktionsapp.

1. I [Azure-portalen](https://portal.azure.com) bläddrar du till din funktionsapp.

1. Under **Inställningar** väljer du **konfiguration**. Leta upp **körnings versionen** på fliken **funktion körnings inställningar** . Observera den aktuella körnings versionen. I exemplet nedan är versionen inställd på `~3`.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Visa körnings versionen." border="true":::

1. Om du vill fästa funktionsappen till version 1.x-körningen väljer du **~ 1** under **Körningsversion**. Den här växeln är inaktiverad när du har funktioner i appen.

1. När du ändrar körningsversionen går du tillbaka till fliken **Översikt** och väljer **Starta om** för att starta om appen.  Funktionsappen startar om och körs på version 1.x-körningen, och version 1.x-mallarna används när du skapar funktioner.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Starta om din Function-app." border="true":::
