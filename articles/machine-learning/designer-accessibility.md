---
title: Använda hjälpmedels funktionerna i designern
titleSuffix: Azure Machine Learning
description: Lär dig mer om kortkommandon och skärm läsarenas hjälpmedels funktioner i designern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893422"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Använd ett tangent bord för Azure Machine Learning designer

Lär dig hur du använder ett tangent bord och en skärm läsare för att använda Azure Machine Learning designer. En lista över kortkommandon som fungerar överallt i Azure Portal finns i [kortkommandon i Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Det här arbets flödet har testats med [skärm läsaren](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) och [JAWS](https://www.freedomscientific.com/products/software/jaws/), men det bör fungera med andra standard skärm läsare.

## <a name="navigate-the-pipeline-graph"></a>Navigera i pipeline-diagrammet

Pipeline-diagrammet är ordnat som en kapslad lista. Den yttre listan är en modul-lista som beskriver alla moduler i pipeline-diagrammet. Den inre listan är en anslutnings lista som beskriver alla anslutningar för en speciell modul.  

1. I listan modul använder du piltangenten för att växla mellan moduler.
1. Använd fliken för att öppna anslutnings listan för modulen.
1. Använd piltangent för att växla mellan anslutnings portarna för modulen.
1. Använd "G" för att gå till modulen mål.

## <a name="edit-the-pipeline-graph"></a>Redigera pipeline-diagrammet

### <a name="add-a-module-to-the-graph"></a>Lägga till en modul i grafen

1. Använd Ctrl + F6 för att växla fokus från arbets ytan till modul trädet.
1. Hitta önskad modul i modulen modul med hjälp av standard TreeView-kontrollen.

### <a name="edit-a-module"></a>Redigera en modul

Så här ansluter du en modul till en annan modul:

1. Använd CTRL + SHIFT + H när du riktar en modul i listan modul för att öppna anslutnings hjälpen.
1. Redigera anslutnings portarna för modulen.

Så här justerar du egenskaper för modul:

1. Använd CTRL + SKIFT + E när du riktar en modul för att öppna modulens egenskaper.
1. Redigera egenskaperna för modulen.

## <a name="navigation-shortcuts"></a>Navigerings gen vägar

| Tryck | Beskrivning |
|-|-|
| Ctrl + F6 | Växla fokus mellan arbets ytan och modulens träd |
| CTRL + F1   | Öppna informations kortet när du fokuserar på en nod i modul träd |
| CTRL + SHIFT + H | Öppna anslutnings hjälpen när fokus är på en nod |
| CTRL + SKIFT + E | Öppna modulens egenskaper när fokus är på en nod |
| Ctrl + G | Flytta fokus till första noden som misslyckades om pipeline-körningen misslyckades |

## <a name="action-shortcuts"></a>Åtgärds gen vägar

Använd följande kortkommandon med åtkomst nyckeln. Mer information om åtkomst nycklar finns i https://en.wikipedia.org/wiki/Access_key .

| Tryck | Action |
|-|-|
| Åtkomst nyckel + R | Kör |
| Åtkomst nyckel + P | Publicera |
| Åtkomst nyckel + C | Klona |
| Åtkomst nyckel + D | Distribuera |
| Åtkomst nyckel + I | Skapa/uppdatera en härlednings pipeline |
| Åtkomst nyckel + B | Skapa/uppdatera pipeline för batch-härledning |
| Åtkomst nyckel + K | Öppna List rutan skapa härlednings förlopp |
| Åtkomst nyckel + U | Öppna List rutan uppdatera härlednings förlopp |
| Åtkomst nyckel + M | Öppna mer (...) listruta |

## <a name="next-steps"></a>Nästa steg

- [Aktivera hög kontrast eller ändra tema](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Hjälpmedels verktyg på Microsoft](https://www.microsoft.com/accessibility)
