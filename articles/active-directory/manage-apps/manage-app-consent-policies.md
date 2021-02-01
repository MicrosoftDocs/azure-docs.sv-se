---
title: Hantera principer för program medgivande i Azure AD
description: Lär dig hur du hanterar inbyggda och anpassade appar för medgivande för att styra när medgivande kan beviljas.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 62a8b48d6b33a92b62bc4c3634794190585615b7
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222038"
---
# <a name="manage-app-consent-policies"></a>Hantera principer för appmedgivande

Med Azure AD PowerShell kan du Visa och hantera principer för program medgivande.

En princip för program medgivande består av en eller flera villkors uppsättningar, inklusive villkors uppsättningar som är noll eller fler. För att en händelse ska kunna beaktas i en princip för en app-princip måste den matcha *minst* en "inkluderar"-villkors uppsättning och får inte matcha *alla* villkors uppsättningar för "exkluderande".

Varje villkors uppsättning består av flera villkor. För att en händelse ska matcha en villkors uppsättning måste *alla* villkor i villkors uppsättningen vara uppfyllda.

Principer för program medgivande där ID: t börjar med "Microsoft-" är inbyggda principer. Några av dessa inbyggda principer används i befintliga inbyggda katalog roller. `microsoft-application-admin`Appens medgivande princip beskriver till exempel de villkor under vilka rollen program administratör och moln program administratör tillåts att bevilja administratörs medgivande för hela klienten. Inbyggda principer kan användas i anpassade katalog roller och för att konfigurera inställningar för användar medgivande, men kan inte redige ras eller tas bort.

## <a name="pre-requisites"></a>Förutsättningar

1. Kontrol lera att du använder [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -modulen. Det här steget är viktigt om du har installerat både [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) -modulen och [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -modulen.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Visa en lista över befintliga principer för program medgivande

Det är en bra idé att börja med att bekanta dig med de befintliga appens medgivande principer i din organisation:

1. Visa en lista med alla principer för program medgivande:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Visa villkors uppsättningar för en princip:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Visa villkors uppsättningar för "exkluderar":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Skapa en anpassad princip för program medgivande

Följ de här stegen för att skapa en anpassad princip för program medgivande:

1. Skapa en ny tom princip för program medgivande.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Lägg till villkors uppsättningar för "inkluderar".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Upprepa det här steget om du vill lägga till fler villkors uppsättningar för "inkludera".

1. Du kan också lägga till villkors uppsättningar "utesluter".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Upprepa det här steget om du vill lägga till fler villkors uppsättningar för "exkludera".

När appens medgivande princip har skapats kan du tillåta att [användarens medgivande](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) omfattas av den här principen.

## <a name="delete-a-custom-app-consent-policy"></a>Ta bort en anpassad princip för program medgivande

1. Följande visar hur du kan ta bort en anpassad princip för program medgivande. **Det går inte att ångra den här åtgärden.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Det går inte att återställa den borttagna appens medgivande principer. Om du råkar ta bort en anpassad princip för program medgivande måste du återskapa principen.

---

### <a name="supported-conditions"></a>Villkor som stöds

I följande tabell visas en lista över de villkor som stöds för principer för program medgivande.

| Villkor | Description|
|:---------------|:----------|
| PermissionClassification | [Behörighets klassificeringen](configure-permission-classifications.md) för behörigheten som beviljas, eller "alla" så att den överensstämmer med behörighets klassificeringen (inklusive behörigheter som inte klassificeras). Standardvärdet är all. |
| PermissionType | Behörighets typen för den behörighet som beviljas. Använd "program" för program behörigheter (t. ex. app-roller) eller "delegerad" för delegerade behörigheter. <br><br>**Obs!** värdet "delegatedUserConsentable" indikerar delegerade behörigheter som inte har kon figurer ATS av API-utgivaren för att kräva administrativt godkännande – det här värdet kan användas i inbyggda behörighets beviljande principer, men kan inte användas i anpassade behörighets beviljande principer. Krävs. |
| ResourceApplication | **AppId** för resurs programmet (t. ex. API) som en behörighet beviljas för, eller "any", för att matcha alla resurs program eller API: er. Standardvärdet är "any". |
| Behörigheter | Listan med behörighets-ID: n för de angivna behörigheterna för att matcha med, eller en lista med det enskilda värdet "all", för att matcha alla behörigheter. Standardvärdet är "alla". <ul><li>Delegerade behörighets-ID: n kan hittas i egenskapen **OAuth2Permissions** för API: s ServicePrincipal-objekt.</li><li>Program behörighets-ID: n kan hittas i egenskapen **AppRoles** för API: s ServicePrincipal-objekt.</li></ol> |
| ClientApplicationIds | En lista med **AppId** -värden för klient programmen som ska matchas med, eller en lista med det enskilda värdet "all" för att matcha alla klient program. Standardvärdet är "alla". |
| ClientApplicationTenantIds | En lista med Azure Active Directory klient-ID: n där klient programmet är registrerat, eller en lista med det enskilda värdet "all" för att matcha med klient program som registrerats i en klient. Standardvärdet är "alla". |
| ClientApplicationPublisherIds | En lista med Microsoft Partner Network-ID: n (MPN) för [verifierade utgivare](../develop/publisher-verification-overview.md) av klient programmet eller en lista med det enskilda värdet "all" för att matcha klient program från valfri utgivare. Standardvärdet är "alla". |
| ClientApplicationsFromVerifiedPublisherOnly | Ange till `$true` att bara matcha på klient program med en [verifierad utgivare](../develop/publisher-verification-overview.md). Ställ in så att `$false` den matchar alla klient program, även om den inte har någon verifierad utgivare. Standardvärdet är `$false`. |

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användar godkännande](configure-user-consent.md)
* [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och att utvärdera medgivande begär Anden](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller hitta svar på dina frågor:
* [Azure AD på Microsoft Q&A](https://docs.microsoft.com/answers/products/)