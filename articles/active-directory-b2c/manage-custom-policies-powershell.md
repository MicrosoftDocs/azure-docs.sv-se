---
title: Hantera anpassade principer med PowerShell
titleSuffix: Azure AD B2C
description: Använd PowerShell-cmdleten Azure Active Directory (Azure AD) för programmerings hantering av dina Azure AD B2C anpassade principer. Skapa, läsa, uppdatera och ta bort anpassade principer med PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928723"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Hantera Azure AD B2C anpassade principer med Azure PowerShell

Azure PowerShell innehåller flera cmdletar för kommando rads-och skriptbaserade anpassade princip hantering i din Azure AD B2C klient. Lär dig hur du använder Azure AD PowerShell-modulen för att:

* Lista de anpassade principerna i en Azure AD B2C-klient
* Hämta en princip från en klient
* Uppdatera en befintlig princip genom att skriva över innehållet
* Ladda upp en ny princip till din Azure AD B2C-klient
* Ta bort en anpassad princip från en klient

## <a name="prerequisites"></a>Krav

* [Azure AD B2C klient organisation](tutorial-create-tenant.md)och autentiseringsuppgifter för en användare i katalogen med rollen [B2C IEF princip administratör](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Anpassade principer](custom-policy-get-started.md) har laddats upp till din klient
* [Modul för för **hands version** av Azure AD PowerShell för diagram](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Ansluta PowerShell-sessionen till B2C-klienten

Om du vill arbeta med anpassade principer i din Azure AD B2C-klient måste du först ansluta PowerShell-sessionen till klienten med hjälp av kommandot [Connect-AzureAD][Connect-AzureAD] .

Kör följande kommando och Ersätt `{b2c-tenant-name}` med namnet på din Azure AD B2C-klient. Logga in med ett konto som har tilldelats rollen [B2C IEF-princip administratör](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) i katalogen.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Exempel på kommandoutdata som visar en lyckad inloggning:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Visa en lista med alla anpassade principer i klienten

Genom att identifiera anpassade principer kan en Azure AD B2C administratör granska, hantera och lägga till affärs logik i deras verksamhet. Använd kommandot [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] för att returnera en lista med ID: n för de anpassade principerna i en Azure AD B2C klient.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Exempel på kommandoutdata:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Hämta en princip

När du har granskat listan med princip-ID: n kan du ange en speciell princip med [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] för att hämta innehållet.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

I det här exemplet hämtas principen med ID *B2C_1A_signup_signin* :

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Om du vill redigera princip innehållet lokalt går du till kommandots utdata till en fil med `-OutputFilePath` argumentet och öppnar sedan filen i din favorit redigerare.

Exempel kommando som skickar utdata till en fil:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Uppdatera en befintlig princip

När du har redigerat en princip fil som du har skapat eller hämtat, kan du publicera den uppdaterade principen till Azure AD B2C med hjälp av kommandot [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Om du utfärdar `Set-AzureADMSTrustFrameworkPolicy` kommandot med ID: t för en princip som redan finns i din Azure AD B2C klient, skrivs innehållet i den principen över.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exempel kommando:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Fler exempel finns i kommando referensen [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

## <a name="upload-a-new-policy"></a>Ladda upp en ny princip

När du gör en ändring i en anpassad princip som körs i produktion kanske du vill publicera flera versioner av principen för återställnings-eller A/B-testnings scenarier. Eller så kanske du vill göra en kopia av en befintlig princip, ändra den med några små ändringar och sedan ladda upp den som en ny princip för användning av ett annat program.

Använd kommandot [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] för att ladda upp en ny princip:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exempel kommando:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Ta bort en anpassad princip

För att upprätthålla livs cykeln för en ren åtgärd rekommenderar vi att du regelbundet tar bort oanvända anpassade principer. Du kanske till exempel vill ta bort gamla princip versioner när du har genomfört en migrering till en ny uppsättning principer och verifierar de nya princip funktionerna. Dessutom kan det vara klokt att ta bort principerna som har skapats som en del av den misslyckade versionen om du försöker publicera en uppsättning anpassade principer och får ett fel meddelande.

Använd kommandot [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] för att ta bort en princip från din klient organisation.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Exempel kommando:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Felsöka princip uppladdning

När du försöker publicera en ny anpassad princip eller uppdatera en befintlig princip, kan felaktig XML-formatering och fel i princip filen arvs kedja orsaka verifierings fel.

Här är till exempel ett försök att uppdatera en princip med innehåll som innehåller felaktig XML-kod (utdata trunkeras för det kortfattat):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Information om hur du felsöker anpassade principer finns i [felsöka Azure AD B2C anpassade principer och identitets miljö](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du använder PowerShell för att distribuera anpassade principer som en del av en pipeline för kontinuerlig integrering/kontinuerlig leverans (CI/CD) finns i [distribuera anpassade principer från en Azure DevOps-pipeline](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
