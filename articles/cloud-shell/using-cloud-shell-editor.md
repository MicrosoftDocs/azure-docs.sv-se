---
title: Använda Azure Cloud Shell redigeraren | Microsoft Docs
description: Översikt över hur du använder Azure Cloud Shell redigeraren.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832343"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Använda Azure Cloud Shell redigeraren

Azure Cloud Shell innehåller ett integrerat fil redigerings program som skapats från Monaco- [redigeraren](https://github.com/Microsoft/monaco-editor)med öppen källkod. Cloud Shell-redigeringsprogrammet stöder funktioner som språkmarkering, kommandopaletten och en filutforskare.

![Cloud Shell redigerare](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Öppna redigeraren

Om du vill kunna skapa och redigera filer enkelt startar du redigeringsprogrammet genom att köra `code .` i Cloud Shell-terminalen. Då öppnas redigeringsprogrammet med den aktiva arbetskatalogen i terminalen.

Om du vill öppna en fil direkt för snabb redigering kör du `code <filename>` för att öppna redigeringsprogrammet utan filutforskaren.

Om du vill öppna redigeringsprogrammet med en knapp i användargränssnittet klickar du på ikonen för `{}`-redigeringsprogrammet i verktygsfältet. Då öppnas redigeringsprogrammet och katalogen `/home/<user>` används som standard i filutforskaren.

## <a name="closing-the-editor"></a>Stänger redigeraren

Om du vill stänga redigeraren öppnar du `...` Åtgärds panelen längst upp till höger i redigeraren och väljer `Close editor` .

![Stäng redigerings programmet](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Kommando palett

Om du vill starta kommando paletten använder du `F1` nyckeln när fokus har angetts i redigeraren. Det går även att öppna kommando paletten via Åtgärds panelen.

![CMD-palett](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Bidra till Monaco-redigeraren

Stöd för språk markering i Cloud Shell-redigeraren stöds via överordnade funktioner i [Monaco-redigerarens](https://github.com/Microsoft/monaco-editor)användning av Monarch-syntaxen. Information om hur du gör bidrag finns i [hand boken för Monaco Contributor](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Nästa steg

- [Försök att använda snabb starten för bash i Cloud Shell](quickstart.md)
- [Visa en fullständig lista över integrerade Cloud Shell-verktyg](features.md)
