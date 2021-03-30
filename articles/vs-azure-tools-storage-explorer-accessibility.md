---
title: Azure Storage Explorer hjälpmedel | Microsoft Docs
description: Förstå tillgängligheten i Azure Storage Explorer. Granska vilka skärm läsare som är tillgängliga, zoomnings funktioner, hög kontrast teman och kortkommandon.
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035493"
---
# <a name="storage-explorer-accessibility"></a>Storage Explorer hjälpmedel

## <a name="screen-readers"></a>Skärm läsare

Storage Explorer stöder användning av en skärm läsare på Windows och Mac. Följande skärm läsare rekommenderas för varje plattform:

Plattform | Skärm läsare
---------|--------------
Windows  | NVDA
Mac      | Berättarröst
Linux    | (skärm läsare stöds inte i Linux)

Om du stöter på ett funktions problem när du använder Storage Explorer, [öppnar du ett problem på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Du kan göra texten i Storage Explorer större genom att zooma in. Om du vill zooma in klickar du på **Zooma** in på Hjälp-menyn. Du kan också använda Hjälp-menyn för att zooma ut och återställa zoomnings nivån till standard nivån.

![Zoomnings alternativ på Hjälp-menyn][0]

Inställningen zooma ökar storleken på de flesta element i användar gränssnittet. Vi rekommenderar också att du aktiverar inställningar för stora text-och zoomnings inställningar för ditt operativ system för att säkerställa att alla UI-element skalas korrekt.

## <a name="high-contrast-themes"></a>Högkontrast teman

Storage Explorer har två hög kontrast teman, **Högkontrast ljusa** och **Högkontrast mörka**. Du kan ändra ditt tema genom att välja i från menyn Hjälp > teman.

![Undermenyn teman][1]

Tema inställningen ändrar färgen på de flesta UI-elementen. Vi rekommenderar också att du även aktiverar ditt OS-matchande hög kontrast tema för att se till att alla element i användar gränssnittet är korrekt färgade.

## <a name="shortcut-keys"></a>Kortkommandon

### <a name="window-commands"></a>Fönster kommandon

Kommando       | Kortkommando
--------------|--------------------
Nytt fönster    | **CTRL + SKIFT + N**
Stäng redigerings programmet  | **Ctrl + F4**
Avsluta          | **CTRL + SKIFT + W**

### <a name="navigation-commands"></a>Navigerings kommandon

Kommando                | Kortkommando
-----------------------|----------------------
Fokus på nästa panel       | **F6**
Fokusera på föregående panel   | **Shift + F6**
Utforskaren               | **CTRL + SKIFT + E**
Kontohantering     | **Ctrl + Skift + A**
Växla sid List        | **Ctrl + B**
Aktivitetslogg           | **Ctrl + Skift + L**
Åtgärder och egenskaper | **Ctrl + Shift + P**
Aktuell redigerare         | **Ctrl + Home**
Nästa redigerare            | **CTRL + PGDN**
Föregående redigerare        | **CTRL + PGUP**

### <a name="zoom-commands"></a>Zooma kommandon

Kommando  | Kortkommando
---------|------------------
Zooma in  | **Kontroll + =**
Zooma ut | **Kontroll +-**

### <a name="blob-and-file-share-editor-commands"></a>Blob-och fil resurs redigerings kommandon

Kommando | Kortkommando
--------|--------------------
Tillbaka    | **ALT + VÄNSTERPIL**
Vidarebefordra | **ALT + HÖGERPIL**
Upp      | **ALT + UPPIL**

### <a name="editor-commands"></a>Redigerings kommandon

Kommando | Kortkommando
--------|------------------
Kopiera    | **CTRL + C**
Klipp ut     | **Kontroll + X**
Klistra in   | **CTRL + V**
Uppdatera  | **Ctrl + R**

### <a name="other-commands"></a>Andra kommandon

Kommando                | Kortkommando
-----------------------|------------------
Växla Utvecklarverktyg | **F12**
Läs in på nytt                 | **Alt + Ctrl + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
