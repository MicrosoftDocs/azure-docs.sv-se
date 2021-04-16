---
title: Snabbstart – Skapa Azure Analysis Services powershell-Azure Analysis Services | Microsoft Docs
description: Den här snabbstarten beskriver hur du skapar en Azure Analysis Services-server med hjälp av PowerShell
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: eb76925e695f92da973fef9cb86d4e936a766587
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531480"
---
# <a name="quickstart-create-a-server---powershell"></a>Snabbstart: Skapa en server – PowerShell

I den här snabbstarten beskrivs hur du använder PowerShell från kommandoraden för att skapa en Azure Analysis Services-server i din Azure-prenumeration.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration:** Gå [till Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
- **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation och du måste ha ett konto i den katalogen. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).
- **Azure PowerShell**. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Information om att installera och uppgradera finns i [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

## <a name="import-azanalysisservices-module"></a>Importera Az.AnalysisServices-modul

Du använder modulen [Az.AnalysisServices](/powershell/module/az.analysisservices) för att skapa en server i din prenumeration. Läs in Az.AnalysisServices-modulen i PowerShell-sessionen.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med hjälp av kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resursgrupp](../azure-resource-manager/management/overview.md) är en logisk container där Azure-resurser distribueras och hanteras som en grupp. När du skapar servern måste du ange en resursgrupp i prenumerationen. Om du inte redan har en resursgrupp kan du skapa en med hjälp av kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i regionen USA, västra.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Skapa en server

Skapa en ny server med hjälp av kommandot [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). I följande exempel skapas en server med namnet myServer i myResourceGroup, i regionen västra USA på nivå D1 (kostnadsfritt) och med philipc@adventureworks.com som serveradministratör.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort servern från prenumerationen med hjälp av kommandot [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). Ta inte bort servern om du fortsätter med andra snabbstartsguider och självstudier i den här samlingen. I följande exempel tar vi bort servern som skapades i föregående steg.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en server i Azure-prenumerationen med hjälp av PowerShell. Nu när du har en server kan du skydda den genom att konfigurera en serverbrandvägg (valfritt). Du kan även lägga till en grundläggande exempeldatamodell till servern direkt från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Snabbstart: Konfigurera serverbrandväggen – portalen](analysis-services-qs-firewall.md)      
