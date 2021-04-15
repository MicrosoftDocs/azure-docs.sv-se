---
title: Konfigurera behörighetsklassificering med Azure AD
description: Lär dig hur du hanterar klassificeringar av delegerad behörighet.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be58f5cd18d32302d1e92f00afb7d7e0aae09410
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374479"
---
# <a name="configure-permission-classifications"></a>Konfigurera behörighetsklassificeringar

Med behörighetsklassificering kan du identifiera vilken effekt olika behörigheter har enligt organisationens principer och riskbedömningar. Du kan till exempel använda behörighetsklassificering i medgivandeprinciper för att identifiera den uppsättning behörigheter som användare tillåts ge sitt medgivande till.

## <a name="manage-permission-classifications"></a>Hantera behörighetsklassificering

För närvarande stöds endast behörighetsklassificeringen "Låg påverkan". Endast delegerade behörigheter som inte kräver administratörsmedgivande kan klassificeras som "Låg påverkan".

> [!TIP]
> De minsta behörigheter som krävs för att göra grundläggande inloggning är , , och , som `openid` `profile` alla är `email` `User.Read` `offline_access` delegerade behörigheter för Microsoft Graph. Med dessa behörigheter kan en app läsa den inloggade användarens fullständiga profilinformation och kan behålla den här åtkomsten även när användaren inte längre använder appen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill klassificera behörigheter med hjälp Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som [global administratör,](../roles/permissions-reference.md#global-administrator) [programadministratör](../roles/permissions-reference.md#application-administrator)eller [molnprogramadministratör](../roles/permissions-reference.md#cloud-application-administrator)
1. Välj **Azure Active Directory**  >  **klassificeringarna Medgivande och** behörighet  >  **för**  >  **företagsprogram**.
1. Välj **Lägg till behörigheter** för att klassificera en annan behörighet som "Låg påverkan".
1. Välj API:et och välj sedan de delegerade behörigheterna.

I det här exemplet har vi klassificerat den minsta uppsättning behörigheter som krävs för enkel inloggning:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Behörighetsklassificering":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda den senaste Förhandsversionen av Azure AD PowerShell-modulen [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)för att klassificera behörigheter. Behörighetsklassificeringarna konfigureras på **ServicePrincipal-objektet** för det API som publicerar behörigheterna.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Lista de aktuella behörighetsklassificeringarna för ett API

1. Hämta **ServicePrincipal-objektet** för API:et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Läs de delegerade behörighetsklassificeringarna för API:et:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klassificera en behörighet som "Låg påverkan"

1. Hämta **ServicePrincipal-objektet** för API:et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Hitta den delegerade behörighet som du vill klassificera:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Ange behörighetsklassificering med hjälp av behörighetsnamn och ID:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Ta bort en delegerad behörighetsklassificering

1. Hämta **ServicePrincipal-objektet** för API:et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Hitta den delegerade behörighetsklassificering som du vill ta bort:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Ta bort behörighetsklassificeringen:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användarmedgivande](configure-user-consent.md)
* [Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och utvärderar begäranden om medgivande](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller få svar på dina frågor:
* [Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)