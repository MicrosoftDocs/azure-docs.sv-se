---
title: Ange livs längd för token
titleSuffix: Microsoft identity platform
description: Lär dig hur du ställer in livs längder för token som utfärdats av Microsoft Identity Platform. Lär dig hur du hanterar en organisations standard princip, skapar en princip för webb inloggning, skapar en princip för en intern app som anropar ett webb-API och hanterar en avancerad princip.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428123"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurera livs längd principer för token (förhands granskning)
Du kan ange livs längden för en åtkomst, SAML eller ID-token som utfärdats av Microsoft Identity Platform. Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen. Mer information finns i [konfigurations bara livs längd för token](active-directory-configurable-token-lifetimes.md).

I det här avsnittet går vi igenom ett gemensamt princip scenario som kan hjälpa dig att införa nya regler för livs längd för token. I exemplet lär du dig hur du skapar en princip som kräver att användare autentiseras oftare i din webbapp.

## <a name="get-started"></a>Kom igång
Gör så här för att komma igång:

1. Ladda ned den senaste [Azure AD PowerShell-modulens offentliga för hands version](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör `Connect` kommandot för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gången du startar en ny session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Om du vill se alla principer som har skapats i din organisation kör du cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Eventuella resultat med definierade egenskaps värden som skiljer sig från de standardvärden som anges ovan är i omfånget för indragningen.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Om du vill se vilka appar och tjänst huvud namn som är länkade till en speciell princip som du har identifierat kör du följande [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) -cmdlet genom att ersätta **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** med något av dina princip-ID: n. Sedan kan du bestämma om du vill konfigurera inloggnings frekvensen för villkorlig åtkomst eller behålla standardinställningarna för Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Om din klient har principer som definierar anpassade värden för konfigurations egenskaperna för uppdatering och sessionstoken, rekommenderar Microsoft att du uppdaterar principerna till värden som motsvarar de standardinställningar som beskrivs ovan. Om inga ändringar görs kommer Azure AD automatiskt att respektera standardvärdena.

## <a name="create-a-policy-for-web-sign-in"></a>Skapa en princip för webb inloggning

I det här exemplet skapar du en princip som kräver att användare autentiseras oftare i din webbapp. Den här principen anger livs längden för åtkomst-/ID-token och den maximala åldern för en Multi-Factor session-token till tjänstens huvud namn för din webbapp.

1. Skapa en livs längd princip för token.

    Den här principen, för webb inloggning, ställer in livs längden för Access/ID-token och maximal ålder för token för en token i två timmar.

    1. Skapa principen genom att köra cmdleten [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Om du vill se den nya principen och hämta principen **ObjectID** kör du cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till tjänstens huvud namn. Du måste också hämta **ObjectID** för tjänstens huvud namn.

    1. Använd cmdleten [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) för att se alla företagets tjänst huvud namn eller ett enda tjänst huvud namn.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. När du har tjänstens huvud namn, kör du cmdleten [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Skapa livs längds principer för token för uppdatering och sessionstoken
> [!IMPORTANT]
> Från och med den 30 januari 2021 kan du inte konfigurera uppdatering och livstid för sessionstoken. Azure Active Directory inte längre att uppdatera konfigurationen och konfigurationen av sessionstoken i befintliga principer.  Nya token som utfärdas efter att befintliga token har gått ut har nu angetts till [standard konfigurationen](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement). Du kan fortfarande konfigurera åtkomst-, SAML-och ID-token för token efter att konfigurationen och sessionens token har tagits ur bruk.
>
> Den befintliga tokens livs längd kommer inte att ändras. När de har gått ut utfärdas en ny token baserat på standardvärdet.
>
> Om du behöver fortsätta att definiera tids perioden innan en användare uppmanas att logga in igen, konfigurerar du inloggnings frekvensen i villkorlig åtkomst. Läs mer om villkorlig åtkomst i [Konfigurera hantering av autentisering med villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="manage-an-organizations-default-policy"></a>Hantera en organisations standard princip
I det här exemplet skapar du en princip som gör det möjligt för användarna att logga in mindre ofta i hela organisationen. Det gör du genom att skapa en token för token för en token för en token som tillämpas i hela organisationen. Principen tillämpas på alla program i din organisation och för varje tjänst huvud konto som inte redan har en princip uppsättning.

1. Skapa en livs längd princip för token.

    1. Ange en token för uppdatering av en faktor till "tills den har återkallats". Token upphör inte förrän åtkomsten har återkallats. Skapa följande princip definition:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Skapa principen genom att köra cmdleten [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Ta bort alla blank steg genom att köra cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Om du vill se den nya principen och hämta principens **ObjectID** kör du följande kommando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Uppdatera principen.

    Du kan bestämma att den första principen som du ställer in i det här exemplet inte är lika strikt som din tjänst kräver. Kör följande kommando för att ange att en token för uppdatering av en enskild faktor upphör att gälla om två dagar:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Skapa en princip för en intern app som anropar ett webb-API
I det här exemplet skapar du en princip som kräver att användare autentiseras mindre ofta. Principen utvärderar också hur lång tid en användare kan vara inaktiv innan användaren måste autentiseras igen. Principen tillämpas på webb-API: et. När den interna appen begär webb-API: t som en resurs, tillämpas den här principen.

1. Skapa en livs längd princip för token.

    1. Om du vill skapa en strikt princip för ett webb-API kör du cmdleten [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Kör följande kommando för att se den nya principen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till ditt webb-API. Du måste också hämta **ObjectID** för ditt program. Använd cmdleten [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) för att hitta appens **ObjectId** eller Använd [Azure Portal](https://portal.azure.com/).

    Hämta **ObjectID** för appen och tilldela principen:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Hantera en avancerad princip
I det här exemplet skapar du några principer för att lära dig hur prioritets systemet fungerar. Du lär dig också hur du hanterar flera principer som tillämpas på flera objekt.

1. Skapa en livs längd princip för token.

    1. Om du vill skapa en organisations standard princip som ställer in livs längden för token för en enskild faktor på 30 dagar kör du cmdleten [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Om du vill se din nya princip kör du cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till ett huvud namn för tjänsten.

    Nu har du en princip som gäller för hela organisationen. Du kanske vill bevara denna 30-dagars princip för en tjänst huvud namn, men ändra organisationens standard princip till den övre gränsen för "tills-REVOKE".

    1. Om du vill se alla företagets tjänst huvud namn använder du cmdleten [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. När du har tjänstens huvud namn, kör du cmdleten [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Ställ in `IsOrganizationDefault` flaggan på false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Skapa en ny organisations standard princip:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Du har nu den ursprungliga principen som är länkad till tjänstens huvud namn och den nya principen anges som organisationens standard princip. Det är viktigt att komma ihåg att principer som tillämpas på tjänstens huvud namn har prioritet över organisationens standard principer.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hanterings funktioner för autentisering](../conditional-access/howto-conditional-access-session-lifetime.md) i Azure AD villkorlig åtkomst.
