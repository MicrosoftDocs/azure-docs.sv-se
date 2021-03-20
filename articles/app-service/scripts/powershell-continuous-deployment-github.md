---
title: 'PowerShell: Distribuera kontinuerligt från GitHub'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du skapar en app med CI/CD från GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0ccb108b55844f822a42a3f1bccbb182a5658289
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89073870"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Skapa en webbapp med kontinuerlig distribution från GitHub

Det här exempel skriptet skapar en webbapp i App Service med dess relaterade resurser och ställer sedan in [kontinuerlig distribution](../deploy-continuous-deployment.md) från en GitHub-lagringsplats. För GitHub-distribution utan kontinuerlig distribution, se [Skapa en webbapp och distribuera kod från GitHub](powershell-deploy-github.md).

Om det behövs installerar du Azure PowerShell med hjälp av den instruktion som finns i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure. Se också till att:

- Programkoden finns i en offentlig eller privat GitHub-lagringsplats som du äger. Om du vill hämta automatiska versioner strukturerar du lagrings platsen enligt tabellen [Förbered din lagrings plats](../deploy-continuous-deployment.md#prepare-your-repository) .
- Du har [skapat en personlig åtkomsttoken i ditt GitHub-konto](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Ändrar en resurs i en resursgrupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
