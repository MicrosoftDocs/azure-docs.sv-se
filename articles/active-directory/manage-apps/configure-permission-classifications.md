---
title: Konfigurera behörighets klassificering med Azure AD
description: Lär dig hur du hanterar delegerade behörighets klassificeringar.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 12a4ffb311e01ebb78b1ae392d1243c5d67eff6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644572"
---
# <a name="configure-permission-classifications"></a>Konfigurera behörighetsklassificeringar

Med behörighets klassificeringar kan du identifiera vilken inverkan olika behörigheter har enligt organisationens principer och riskbedömningar. Du kan till exempel använda behörighets klassificeringar i principer för medgivande för att identifiera den uppsättning behörigheter som användare tillåts att samtycka till.

## <a name="manage-permission-classifications"></a>Hantera behörighets klassificeringar

För närvarande stöds inte behörighets klassificeringen "låg effekt". Endast delegerade behörigheter som inte kräver administratörs medgivande kan klassificeras som "låg påverkan".

> [!TIP]
> De lägsta behörigheter som krävs för grundläggande inloggning är `openid` ,, `profile` `email` `User.Read` och `offline_access` , som är alla delegerade behörigheter för Microsoft Graph. Med dessa behörigheter kan en app läsa fullständig profil information för den inloggade användaren och kunna upprätthålla den här åtkomsten även om användaren inte längre använder appen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill klassificera behörigheter med hjälp av Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../roles/permissions-reference.md#global-administrator), [program administratör](../roles/permissions-reference.md#application-administrator)eller [moln program administratör](../roles/permissions-reference.md#cloud-application-administrator)
1. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och behörighets**  >  **klassificering**.
1. Välj **Lägg till behörigheter** för att klassificera en annan behörighet som "låg påverkan".
1. Välj API och välj sedan de delegerade behörigheterna.

I det här exemplet har vi klassificerat den minsta behörighets uppsättningen som krävs för enkel inloggning:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Behörighets klassificering":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda den senaste Azure AD PowerShell Preview-modulen, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), för att klassificera behörigheter. Behörighets klassificeringar konfigureras på **ServicePrincipal** -objektet för API: et som publicerar behörigheterna.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Lista de aktuella behörighets klassificeringarna för ett API

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Läs klassificeringarna för delegerad behörighet för API: et:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klassificera en behörighet som "låg påverkan"

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Sök efter den delegerade behörighet som du vill klassificera:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Ange behörighets klassificeringen med hjälp av behörighets namnet och ID: t:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Ta bort en delegerad behörighets klassificering

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Hitta den delegerade behörighets klassificering som du vill ta bort:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Ta bort behörighets klassificeringen:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användar godkännande](configure-user-consent.md)
* [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och att utvärdera medgivande begär Anden](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller hitta svar på dina frågor:
* [Azure AD på Microsoft Q&A](/answers/topics/azure-active-directory.html)