---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070222"
---
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd Azure PowerShell [New-AzResourceGroup-](/powershell/module/az.resources/new-azresourcegroup) cmdlet: en för att skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
