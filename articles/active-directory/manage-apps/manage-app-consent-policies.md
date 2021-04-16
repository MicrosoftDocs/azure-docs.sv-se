---
title: Hantera principer för appmedgivande i Azure AD
description: Lär dig hur du hanterar inbyggda och anpassade principer för appmedgivande för att kontrollera när medgivande kan beviljas.
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
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375703"
---
# <a name="manage-app-consent-policies"></a>Hantera principer för appmedgivande

Med Azure AD PowerShell kan du visa och hantera principer för appmedgivande.

En appmedgivandeprincip består av en eller flera "inkluderar" villkorsuppsättningar och noll eller flera villkorsuppsättningar som "undantas". För att en händelse ska beaktas i en  appmedgivandeprincip måste den matcha minst en "inkluder"-villkorsuppsättning och får inte matcha några  "exkluderar"-villkorsuppsättning.

Varje villkorsuppsättning består av flera villkor. För att en händelse ska matcha en *villkorsuppsättning* måste alla villkor i villkorsuppsättningen vara uppfyllda.

Principer för appmedgivande där ID:t börjar med "microsoft-" är inbyggda principer. Vissa av dessa inbyggda principer används i befintliga inbyggda katalogroller. Principen för appmedgivande beskriver till exempel de villkor under vilka rollerna Programadministratör och Molnprogramadministratör tillåts bevilja administratörsmedgivande för `microsoft-application-admin` hela klientorganisationen. Inbyggda principer kan användas i anpassade katalogroller och för att konfigurera inställningar för användarmedgivande, men de kan inte redigeras eller tas bort.

## <a name="pre-requisites"></a>Förutsättningar

1. Kontrollera att du använder modulen [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Det här steget är viktigt om du har installerat både [AzureAD-modulen](/powershell/module/azuread/) och [AzureADPreview-modulen).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Visa en lista över befintliga principer för appmedgivande

Det är en bra idé att börja med att bekanta dig med de befintliga appmedgivandeprinciperna i din organisation:

1. Lista alla principer för appmedgivande:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Visa villkorsuppsättningarna "inkluderar" för en princip:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Visa villkorsuppsättningarna "undantar":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Skapa en anpassad appmedgivandeprincip

Följ dessa steg för att skapa en anpassad appmedgivandeprincip:

1. Skapa en ny tom appmedgivandeprincip.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Lägg till villkorsuppsättningar för "includes".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Upprepa det här steget för att lägga till ytterligare "inkludera"-villkorsuppsättningar.

1. Du kan också lägga till villkorsuppsättningar som "undantar".

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

   Upprepa det här steget om du vill lägga till ytterligare villkorsuppsättningar för "exkludera".

När principen för appmedgivande har skapats kan du tillåta [användarens medgivande enligt](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) den här principen.

## <a name="delete-a-custom-app-consent-policy"></a>Ta bort en anpassad appmedgivandeprincip

1. Följande visar hur du kan ta bort en anpassad appmedgivandeprincip. **Det går inte att ångra den här åtgärden.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Det går inte att återställa borttagna principer för appmedgivande. Om du av misstag tar bort en anpassad appmedgivandeprincip måste du skapa principen på nytt.

---

### <a name="supported-conditions"></a>Villkor som stöds

Följande tabell innehåller en lista över villkor som stöds för appmedgivandeprinciper.

| Villkor | Beskrivning|
|:---------------|:----------|
| PermissionClassification | [Behörighetsklassificeringen](configure-permission-classifications.md) för behörigheten som beviljas, eller "alla" för att matcha med en behörighetsklassificering (inklusive behörigheter som inte har klassificerats). Standardvärdet är "all". |
| PermissionType | Behörighetstypen för behörigheten som beviljas. Använd "program" för programbehörigheter (t.ex. approller) eller "delegerad" för delegerade behörigheter. <br><br>**Obs!** Värdet "delegatedUserConsentable" anger delegerade behörigheter som inte har konfigurerats av API-utgivaren för att kräva administratörsmedgivande – det här värdet kan användas i inbyggda principer för behörighetstilldelegering, men kan inte användas i anpassade behörighetstilldelade principer. Krävs. |
| ResursApplication | **AppId för** resursprogrammet (t.ex. API:et) som en behörighet beviljas för, eller "any" för att matcha med alla resursprogram eller API:er. Standardvärdet är "any". |
| Behörigheter | Listan över behörighets-ID:er för de specifika behörigheter som ska matchas med, eller en lista med det enskilda värdet "alla" för att matcha med vilken behörighet som helst. Standardvärdet är "alla". <ul><li>Delegerade behörighets-ID:n finns **i egenskapen OAuth2Permissions** för API:et ServicePrincipal-objektet.</li><li>Programbehörighets-ID:n finns i **egenskapen AppRoles** för API:et ServicePrincipal-objekt.</li></ol> |
| ClientApplicationIds | En lista med **AppId-värden** som klientprogrammen ska matcha med, eller en lista med det enskilda värdet "alla" för att matcha alla klientprogram. Standardvärdet är "alla". |
| ClientApplicationTenantIds | En lista Azure Active Directory klientorganisations-ID:er där klientprogrammet är registrerat, eller en lista med det enda värdet "alla" för att matcha med klientappar som registrerats i en klientorganisation. Standardvärdet är "alla". |
| ClientApplicationPublisherIds | En lista Microsoft Partner Network (MPN) för verifierade utgivare av klientprogrammet, eller en lista med det enda värdet "alla" som ska [matchas](../develop/publisher-verification-overview.md) med klientappar från valfri utgivare. Standardvärdet är "alla". |
| ClientApplicationsFromVerifiedPublisherOnly | Ange till `$true` för att endast matcha på klientprogram med en [verifierad utgivare](../develop/publisher-verification-overview.md). Ange till `$false` för att matcha i alla klientprogram, även om den inte har en verifierad utgivare. Standardvärdet är `$false`. |

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användarmedgivande](configure-user-consent.md)
* [Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och utvärderar begäranden om medgivande](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller få svar på dina frågor:
* [Azure AD på Microsoft Q&A](/answers/products/)