---
title: 'Använd PowerShell för att skapa en Azure AD-App för att få åtkomst till Azure Media Services-API: t | Microsoft Docs'
description: Lär dig hur du använder PowerShell för att skapa en Azure Active Directory-app (Azure AD) och konfigurera den för att få åtkomst till Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1be3de87493db61ebc901fe240b9eb109a3a90a3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695162"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Använd PowerShell för att skapa en Azure AD-app som ska användas med Azure Media Services-API: et

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Lär dig hur du använder ett PowerShell-skript för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvud namn för att få åtkomst till Azure Media Services resurser.  

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto i Azure Portal](media-services-portal-create-account.md).

- Azure PowerShell. Mer information finns i [så här använder du Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Skapa en Azure AD-App med hjälp av PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Mer information finns i följande artiklar:

- [Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Konfigurera daemon-appar manuellt med hjälp av certifikat](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
