---
title: 'PowerShell: Ladda upp och bind TLS/SSL'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du binder ett anpassat TLS/SSL-certifikat till en app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 74bec2e8793331019a6eca986446880c10565aeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079956"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Binda ett anpassat TLS/SSL-certifikat till en webbapp med hjälp av PowerShell

Det här exempel skriptet skapar en webbapp i App Service med dess relaterade resurser och binder sedan TLS/SSL-certifikatet för ett anpassat domän namn till den. 

Om det behövs installerar du Azure PowerShell med hjälp av den instruktion som finns i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure. Se också till att:

- En anslutning har skapats med Azure med hjälp av kommandot `az login`.
- Du har åtkomst till din domänregistrators DNS-konfigurationssida.
- Du är giltig. PFX-fil och lösen ord för det TLS/SSL-certifikat som du vill ladda upp och binda.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Ändrar en App Service-plan för att ändra prisnivån. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Ändrar konfigurationen för en webbapp. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Skapar en TLS/SSL-certifikat bindning för en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
