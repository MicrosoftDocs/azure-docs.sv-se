---
title: 'PowerShell-skript: övervaka användningen av en Azure-Dataresurs'
description: Det här PowerShell-skriptet hämtar användnings statistik för en data resurs som skickats.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: e9ff7a29cba9b8e9ca058bfe742f484c5b495cd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221323"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Använd PowerShell för att övervaka användningen av en data resurs som skickats

Det här PowerShell-skriptet övervakar data användningen genom att ange synkroniseringarna för en data resurs som skickats och hämta information om en speciell synkronisering.

## <a name="sample-script"></a>Exempelskript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization) | Lista synkroniseringar på en resurs. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail) | Hämtar synkroniseringsinformation för en resurs-synkronisering. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
