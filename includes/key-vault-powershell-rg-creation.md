---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070222"
---
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd Azure PowerShell [New-AzResourceGroup-](/powershell/module/az.resources/new-azresourcegroup) cmdlet: en för att skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
