---
title: Dirigera trafik för HA av program – Azure PowerShell-Traffic Manager
description: Azure PowerShell skript exempel – väg trafik för hög tillgänglighet för program
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: 114041a6ad0b311a1e01a692b9e6c36b0a8fb900
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185384"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Dirigera trafik för hög tillgänglighet för program som använder Azure PowerShell

Det här skriptet skapar en resurs grupp, två App Service-planer, två webb program, en Traffic Manager-profil och två Traffic Manager-slutpunkter. Traffic Manager dirigerar trafik till programmet i en region som den primära regionen och till den sekundära regionen när programmet i den primära regionen inte är tillgängligt. Innan du kör skriptet måste du ändra värdena för MyWebAppL1 och MyWebAppL2 till unika värden i Azure. När du har kört skriptet kan du komma åt appen i den primära regionen med URL-mywebapp.trafficmanager.net.

Om det behövs installerar du Azure PowerShell med hjälp av den instruktion som finns i [Azure PowerShell-guiden](/powershell/azure)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en webbapp, en Traffic Manager-profil och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. Detta är som en Server grupp för din Azure-webbapp. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en Azure-webbapp i App Service plan. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Skapar en Azure-webbapp i App Service plan. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Skapar en Azure Traffic Manager-profil. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Lägger till en slutpunkt i en Azure Traffic Manager-profil. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).