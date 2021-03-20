---
title: 'PowerShell-skript: lista Azure Data Share-inbjudningar som skickas till en konsument'
description: Lär dig hur det här PowerShell-skriptet får inbjudningar som skickas till en konsument och se ett exempel på det skript som du kan använda.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221221"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Använd PowerShell för att hämta en inbjudan till data resursen

Det här PowerShell-skriptet hämtar inbjudningar som skickas till en konsument.

## <a name="sample-script"></a>Exempelskript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Hämta och visa en lista över inbjudningar till skickade data resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
