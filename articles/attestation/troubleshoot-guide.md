---
title: Felsökningsguide för Azure Attestation
description: Fel söknings guide för de problem som observerats ofta
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704324"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure fel söknings guide för attestering

Fel hantering i Azure-attestering implementeras i [rikt linjerna för Microsoft REST API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). Det fel svar som returneras av API: erna för Azure attestering innehåller HTTP-statuskod och namn/värde-par med namnen "Code" och "Message". Värdet för kod är läsligt och är en indikator för typen av fel. Värdet "Message" avser att hjälpa användaren och ger fel information.

Om problemet inte åtgärdas i den här artikeln kan du även skicka en support förfrågan för Azure på [support sidan för Azure](https://azure.microsoft.com/support/options/).

Nedan visas några exempel på de fel som returneras av Azure-attestering:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: otillåtet undantag

### <a name="http-status-code"></a>HTTP-statuskod
401

**Felkod** Tillstånd

**Scenario exempel**
  - Attesterings problem om användaren inte har tilldelats rollen som attesterings läsare
  - Det går inte att hantera attesterings principer eftersom användaren inte har tilldelats lämpliga roller
  - Det går inte att hantera signerings princip för attestering eftersom användaren inte har tilldelats lämpliga roller

Användare med läsar roll som försöker redigera en attesterings princip i PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Fel söknings steg**

För att kunna visa attesterings principer/princip undertecknare kräver en Azure AD-användare behörigheten för "åtgärder":
- Microsoft. attestering/attestationProviders/attestering/läsning

  Den här behörigheten kan tilldelas till en AD-användare via en roll som "ägare" (behörighet som jokertecken) eller "läsare" (behörigheter för jokertecken) eller "attesterings läsare" (endast vissa behörigheter för Azure-attestering).

För att kunna lägga till/ta bort princip undertecknare eller konfigurera principer, kräver en Azure AD-användare följande behörigheter för "åtgärder":
- Microsoft. attestering/attestationProviders/attestering/skrivning
- Microsoft. attestering/attestationProviders/attestering/Delete

  Dessa behörigheter kan tilldelas till en AD-användare via en roll som "ägare" (behörigheter för jokertecken), "deltagare" (behörigheter för jokertecken) eller "attesterings bidrags givare" (endast vissa behörigheter för Azure-attestering).

Kunder kan välja att använda standardprovidern för attestering eller skapa egna leverantörer med anpassade principer. Om du vill skicka begäran om attestering till anpassade attesterings leverantörer krävs "ägare" (behörigheter för jokertecken) eller "läsare" (behörigheter för jokertecken) eller "attesterings läsare" för användaren. Standard leverantörerna är tillgängliga för alla Azure AD-användare.

Verifiera rollerna i PowerShell genom att köra följande:

a. Starta PowerShell och logga in på Azure via cmdleten "Connect-AzAccount"

b. Verifiera dina roll tilldelnings inställningar för Azure


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Du bör se något som liknar följande:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Om du inte hittar en lämplig roll tilldelning i listan följer du anvisningarna i [här](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP – 400 fel

### <a name="http-status-code"></a>HTTP-statuskod
400

Det finns olika orsaker till varför en begäran kan returnera 400. Nedan visas några exempel på fel som returneras av Azure attesterings-API: er:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Attesterings fel på grund av princip utvärderings fel

Attesterings principen inkluderar auktoriseringsregler och utgivnings regler. Enklaven-beviset utvärderas baserat på auktoriseringsregler. Utfärdande regler definierar de anspråk som ska tas med i attesterings-token. Om anspråk i enklaven-bevis inte är kompatibla med auktoriseringsregler, kommer attesterings anrop att returnera princip utvärderings fel. 

**Felkod** PolicyEvaluationError

**Scenario exempel** När anspråk i enklaven-offerten inte stämmer överens med auktoriseringsregler för policyn för attestering

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Fel söknings steg** Användare kan utvärdera enklaven-bevis mot en princip för SGX-attestering innan du konfigurerar samma.

Skicka en begäran till attesterings-API genom att ange princip text i parametern "draftPolicyForAttestation". AttestSgxEnclave-API: et kommer att använda det här princip dokumentet under attesterings anropet och kan användas för att testa attesterings principer innan de används. Attesterings-token som genererades när det här fältet finns blir oskyddat.

Se [exempel på attesterings princip](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Attesteringen kunde inte utföras på grund av ogiltiga ininformation

**Felkod** InvalidParameter

**Scenario exempel** Fel i SGX-attestering på grund av ogiltiga indatatyper. Nedan visas några exempel på fel meddelanden:
- Den angivna offerten var ogiltig på grund av ett fel i offertens säkerhet 
- Den angivna offerten var ogiltig eftersom den enhet där offerten genererades inte uppfyller kraven för Azures bas linje
- Den angivna offerten var ogiltig eftersom den TCBInfo eller QEID som tillhandahölls av PCK Cache Service var ogiltig

**Fel söknings steg**

Microsoft Azure attestering stöder attestering av SGX-offerter som genererats av Intel SDK och öppen enklaven SDK.

Se [kod exempel](/samples/browse/?expanded=azure&terms=attestation) för att utföra attestering med Open enklaven SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Ogiltigt certifikat kedje fel vid uppladdning av princip-/princip undertecknare

**Felkod** InvalidParameter

**Scenario exempel** Konfigurera signerad princip eller Lägg till/ta bort princip undertecknare, som är signerad med en ogiltig certifikat kedja (till exempel när det grundläggande begränsnings tillägget för rot certifikatet inte är inställt på subject-typ = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Fel söknings steg** Rot certifikatet måste flaggas som utfärdat av en CA (Basic-begränsningarna för X. 509), annars betraktas det inte som ett giltigt certifikat. 

Kontrol lera att rot certifikatets tillägg för grundläggande begränsningar är inställt på att ange ämnes typen = CA

Annars anses certifikat kedjan vara ogiltig.

Se [princip undertecknare](./policy-signer-examples.md) och [princip](./policy-examples.md) exempel 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Lägg till/ta bort princip signerings problem

**Felkod** InvalidOperation

**Scenario exempel**

När användaren laddar upp JWS utan "Maa-policyCertificate"-anspråk

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

När användaren inte överför ett certifikat i JWS-format

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Fel söknings steg** Om du vill lägga till/ta bort ett nytt princip signerings certifikat använder du RFC7519 JSON Web Token (JWT) med ett anspråk med namnet "x-MS-policyCertificate". Värdet för anspråket är en RFC7517 JSON-webbnyckel som innehåller det certifikat som ska läggas till. JWT måste vara signerat med en privat nyckel för alla giltiga princip registrerings certifikat som är associerade med providern. Se [exempel på princip registrering](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Konfigurations problem för attesterings princip

**Felkod** PolicyParsingError

**Scenario exempel** Principen tillhandahölls med felaktig syntax (till exempel saknas semikolon)/valid JWT-princip)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Felkod** InvalidOperation

**Scenario exempel** Ogiltigt innehåll har angetts (till exempel Ladda upp princip/osignerad princip när princip signering krävs)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Fel söknings steg** Se till att principen i text format är UTF-8-kodad.

Om princip signering krävs måste attesterings principen bara konfigureras i formatet RFC7519 JSON Web Token (JWT). Om princip signering inte krävs kan du konfigurera en princip i text-eller JWT-format.

Om du vill konfigurera en princip i JWT-format använder du JWT med ett anspråk med namnet "AttestationPolicy". Värdet för anspråket är Base64URL-kodad version av princip texten. Om attesteringsservern har kon figurer ATS med princip signerings certifikat, måste JWT signeras med en privat nyckel för alla giltiga certifikat signerings certifikat som är associerade med providern. 

Om du vill konfigurera en princip i text format anger du princip texten direkt.

I PowerShell anger du PolicyFormat som JWT för att konfigurera principen i JWT-format. Standard princip formatet är text.

Se exempel på attesterings [princip](./policy-examples.md) och [hur du skapar en princip för attestering](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. AZ. installations problem för attestering i PowerShell

Det gick inte att installera AZ eller AZ. attesterings-moduler i PowerShell

### <a name="error"></a>Fel

Varning! det går inte att matcha paket källans https://www.powershellgallery.com/api/v2 PackageManagement\Install-Package: ingen matchning hittades för de angivna Sök kriterierna och modulnamnet

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

PowerShell-galleriet har inaktuella Transport Layer Security (TLS) version 1,0 och 1,1. 

TLS 1,2 eller en senare version rekommenderas. 

Om du vill fortsätta att interagera med PowerShell-galleriet kör du följande kommando innan du Install-Module kommandon

**[Net. ServicePointManager]:: SecurityProtocol = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. princip åtkomst/konfigurations problem i PowerShell

Användaren har tilldelats lämpliga roller. Men problem med att drabbas vid hantering av attesterings principer via PowerShell.

### <a name="error"></a>Fel
Klienten med objekt-ID &lt; objekt-ID &gt;  har inte behörighet att utföra åtgärden Microsoft. Authorization/RoleAssignments/Write över omfånget///Write över omfånget/ &lt; subscriptionId &gt; ResourceGroups/secure_enclave_poc/providers/Microsoft.Authorization/RoleAssignments/ &lt; Role assignmentId &gt; eller så är omfattningen ogiltigt. Om åtkomst nyligen har beviljats ska du uppdatera dina autentiseringsuppgifter

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Den minsta versionen av AZ-moduler som krävs för att ge stöd för attestering är följande: 

 **AZ 4.5.0** 
 
 **AZ. Accounts 1.9.2**
 
 **AZ. attestering 0.1.8** 

Kör kommandot nedan för att kontrol lera den installerade versionen av alla AZ-moduler 

```powershell
Get-InstalledModule 
```

Om versionerna inte matchar minimi kravet kör Update-Module-kommandon

t. ex.-Update-Module-Name AZ. attestering
