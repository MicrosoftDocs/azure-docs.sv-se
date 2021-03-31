---
title: Azure PowerShell-prenumerera på resurs grupp
description: Den här artikeln innehåller ett exempel på Azure PowerShell-skript som visar hur du prenumererar på Event Grid händelser för en resurs grupp och ett filter för en resurs.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f3aeec208ba6b80643e50307b89f590977bfb446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460770"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Prenumerera på händelser för en resursgrupp och filtrera efter en resurs med PowerShell

Det här skriptet skapar en Event Grid-prenumeration på händelser för en resursgrupp. Det använder ett filter för att endast hämta händelser för en specifik resurs i resursgruppen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Exempelskript – stabilt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Exempelskript – modul för förhandsversion

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Förhandsversionen av exempelskriptet kräver Event Grid-modulen. Installera genom att köra `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa händelseprenumerationen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Skapa en Event Grid-prenumeration. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/get-started-azureps).
