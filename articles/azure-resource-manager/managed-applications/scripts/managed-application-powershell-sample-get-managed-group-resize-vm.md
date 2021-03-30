---
title: Hämta hanterade resurs grupper & ändra storlek på virtuella datorer – Azure PowerShell
description: Innehåller Azure PowerShell exempel skript som hämtar en hanterad resurs grupp för ett Azure-hanterat program. Skriptet ändrar storlek på virtuella datorer.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86055995"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Hämta resurser från en hanterad resursgrupp och ändra storleken på virtuella datorer med PowerShell

Detta skript hämtar resurser från en hanterad resursgrupp och ändrar storlek på de virtuella datorerna i resursgruppen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | Visar en lista med hanterade program. Ange resursgruppens namn för att förfina resultatet. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Visar resurserna. Ange en resursgrupp och en resurstyp för att förfina resultatet. |
| [Update-AzVM](/powershell/module/az.compute/update-azvm) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/get-started-azureps).
