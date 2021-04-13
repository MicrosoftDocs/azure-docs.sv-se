---
title: Ange livslängder för token
titleSuffix: Microsoft identity platform
description: Lär dig hur du anger livslängder för token som utfärdats av Microsoft Identity Platform. Lär dig hur du hanterar en organisations standardprincip, skapar en princip för webbin inloggning, skapar en princip för en inbyggd app som anropar ett webb-API och hanterar en avancerad princip.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363894"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurera livslängdsprinciper för token (förhandsversion)
Du kan ange livslängden för en åtkomst-, SAML- eller ID-token som utfärdats av Microsoft Identity Platform. Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen. Mer information finns i [konfigurerbara livslängder för token.](active-directory-configurable-token-lifetimes.md)

I det här avsnittet går vi igenom ett vanligt principscenario som kan hjälpa dig att införa nya regler för tokenlivslängd. I det här exemplet får du lära dig hur du skapar en princip som kräver att användarna autentiserar oftare i webbappen.

## <a name="get-started"></a>Kom igång

Kom igång genom att ladda ned den senaste offentliga [förhandsversionen av Azure AD PowerShell-modulen.](https://www.powershellgallery.com/packages/AzureADPreview)

Kör sedan kommandot för `Connect` att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång du startar en ny session.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Skapa en princip för webb inloggning

I det här exemplet skapar du en princip som kräver att användarna autentiserar oftare i webbappen. Den här principen anger livslängden för åtkomst-/ID-token till tjänstens huvudnamn för din webbapp.

1. Skapa en livslängdsprincip för token.

    Den här principen för webb inloggning anger livslängden för åtkomst/ID-token till två timmar.

    Skapa principen genom att köra cmdleten [New-AzureADPolicy:](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Om du vill se den nya principen och hämta **objectId** för principen kör du cmdleten [Get-AzureADPolicy:](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta **ObjectId för tjänstens** huvudnamn.

    Använd [cmdleten Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) för att se alla organisationens tjänsthuvudnamn eller ett enda huvudnamn för tjänsten.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    När du har tjänstens huvudnamn kör du [cmdleten Add-AzureADServicePrincipalPolicy:](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true)

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Visa befintliga principer i en klientorganisation

Om du vill se alla principer som har skapats i din organisation kör du cmdleten [Get-AzureADPolicy.](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true)  Resultat med definierade egenskapsvärden som skiljer sig från de standardvärden som anges ovan ligger inom omfånget för tillbakagången.

```powershell
Get-AzureADPolicy -All $true
```

Om du vill se vilka appar och tjänstens huvudnamn som är länkade till en specifik princip som du har identifierat kör du följande cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) genom att ersätta **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** med någon av dina princip-ID:n. Sedan kan du bestämma om du vill konfigurera inloggningsfrekvensen för villkorsstyrd åtkomst eller stanna kvar med standardinställningarna för Azure AD.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Om din klientorganisation har principer som definierar anpassade värden för konfigurationsegenskaperna för uppdaterings- och sessionstoken rekommenderar Microsoft att du uppdaterar dessa principer till värden som återspeglar de standardvärden som beskrivs ovan. Om inga ändringar görs kommer Azure AD automatiskt att respektera standardvärdena.

### <a name="troubleshooting"></a>Felsökning
Vissa användare har rapporterat ett `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` fel när `Get-AzureADPolicy` cmdleten har körts. Som en tillfällig lösning kan du köra följande för att avinstallera/installera om AzureAD-modulen och sedan installera AzureADPreview-modulen:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer [om funktioner för hantering av autentiseringssessioner](../conditional-access/howto-conditional-access-session-lifetime.md) i villkorsstyrd åtkomst i Azure AD.
