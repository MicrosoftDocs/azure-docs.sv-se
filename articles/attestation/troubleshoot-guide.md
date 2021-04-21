---
title: Felsökningsguide för Azure Attestation
description: Felsökningsguide för vanliga problem
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d3e34bee3d0f1420b379638389e6fad0a2fed60
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831572"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>felsökningsguide Microsoft Azure attestation

Felhantering i Azure Attestation implementeras enligt [Microsofts REST API riktlinjer.](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses) Felsvaret som returneras av Azure Attestation-API:er innehåller HTTP-statuskod och namn/värde-par med namnen "code" och "message". Värdet för "kod" är läsbart för människor och är en indikator på typen av fel. Värdet för "meddelande" har för avsikt att hjälpa användaren och ger felinformation.

Om problemet inte åtgärdas i den här artikeln kan du också skicka en Azure-supportbegäran på [Azure-supportsidan.](https://azure.microsoft.com/support/options/)

Nedan visas några exempel på fel som returneras av Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP–401: Otillåtet undantag

### <a name="http-status-code"></a>HTTP-statuskod
401

**Felkod** Obehörig

**Scenarioexempel**
  - Det går inte att hantera attestationsprinciper eftersom användaren inte har tilldelats lämpliga roller
  - Det går inte att hantera attestationsprincipens signerare eftersom användaren inte har tilldelats lämpliga roller

Användare med rollen Läsare som försöker redigera en attestationsprincip i PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Felsökningsanvisningar**

För att kunna hantera principer kräver en Azure AD-användare följande behörigheter för "Åtgärder":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  För att utföra de här åtgärderna måste en Azure AD-användare ha rollen "Attestation Contributor" (Attestation-deltagare) på attestationsprovidern. Dessa behörigheter kan också ärvas med roller som "Ägare" (behörigheter med jokertecken), "Deltagare" (behörigheter med jokertecken) i prenumerationen/resursgruppen.  

För att kunna läsa principer kräver en Azure AD-användare följande behörighet för "Åtgärder":
- Microsoft.Attestation/attestationProviders/attestation/read

  För att utföra den här åtgärden måste en Azure AD-användare ha rollen "Attestation Reader" på attestationsprovidern. Läsbehörigheten kan också ärvas med roller som "läsare" (behörigheter med jokertecken) i prenumerationen/resursgruppen.  

Kontrollera rollerna i PowerShell genom att köra stegen nedan:

a. Starta PowerShell och logga in på Azure via cmdleten "Connect-AzAccount"

b. Se vägledningen här [för att](../role-based-access-control/role-assignments-list-powershell.md) verifiera din Azure-rolltilldelning på attestationsprovidern

c. Om du inte hittar en lämplig rolltilldelning följer du anvisningarna [här](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP – 400-fel

### <a name="http-status-code"></a>HTTP-statuskod
400

Det finns olika orsaker till varför en begäran kan returnera 400. Nedan visas några exempel på fel som returneras av Azure Attestation-API:er:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Attestationsfel på grund av principutvärderingsfel

Attestationsprincipen innehåller auktoriseringsregler och utfärdanderegler. Enklavbevis utvärderas baserat på auktoriseringsreglerna. Utfärdanderegler definierar de anspråk som ska ingå i attestationstoken. Om anspråk i enklavbevis inte följer auktoriseringsreglerna returnerar attestanrop principutvärderingsfel. 

**Felkod** PolicyEvaluationError

**Scenarioexempel** När anspråk i enklavcitat inte matchar auktoriseringsreglerna för attereringsprincipen

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Felsökningssteg** Användare kan utvärdera enklavbevis mot en SGX-attestationsprincip innan de konfigurerar samma.

Skicka en begäran om attesterings-API genom att tillhandahålla principtext i parametern "draftPolicyForAttestation". API:et AttestSgxEnclave använder det här principdokumentet under attest-anropet och det kan användas för att testa attesteringsprinciper innan de förbrukas. Den attestationstoken som genereras när det här fältet finns är oskyddad.

Se [exempel på attestationprincip](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Attestationsfel på grund av ogiltiga indata

**Felkod** InvalidParameter

**Scenarioexempel** SGX-attestationsfel på grund av ogiltiga indata. Nedan visas några exempel på felmeddelanden:
- Det angivna offerten var ogiltig på grund av ett fel i offerten 
- Angivet citattecken var ogiltigt eftersom enheten där offerten genererades inte uppfyller Azure-baslinjekraven
- Det angivna citattet var ogiltigt eftersom TCBInfo eller QEID som tillhandahölls av PCK Cache Service var ogiltigt

**Felsökningsanvisningar**

Microsoft Azure attestation stöder attestation av SGX-citattecken som genererats av Intel SDK och Open Enclave SDK.

Se [kodexempel för](/samples/browse/?expanded=azure&terms=attestation) att utföra attestation med Hjälp av Open Enclave SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Ogiltigt certifikatkedjefel vid uppladdning av princip-/principloggare

**Felkod** InvalidParameter

**Scenarioexempel** Konfigurera signerad princip eller lägg till/ta bort princip signerad, som är signerad med en ogiltig certifikatkedja (till exempel när tillägget Grundläggande begränsningar för rotcertifikatet inte har angetts till Ämnestyp = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Felsökningssteg** Rotcertifikatet måste flaggas som utfärdat av en certifikatutfärdare (de grundläggande X.509-begränsningarna), annars betraktas det inte som ett giltigt certifikat. 

Se till att tillägget Grundläggande begränsningar för rotcertifikatet är inställt för att ange ämnestyp = CA

Annars anses certifikatkedjan vara ogiltig.

Se [exempel på principtecken](./policy-signer-examples.md) [och](./policy-examples.md) princip 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Fel med att lägga till/ta bort princip signerare

**Felkod** InvalidOperation

**Scenarioexempel**

När användaren laddar upp JWS utan anspråket "maa-policyCertificate"

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

**Felsökningssteg** Om du vill lägga till/ta bort ett nytt principcertifikat använder du RFC7519 JSON Web Token (JWT) med ett anspråk med namnet "x-ms-policyCertificate". Anspråksvärdet är en RFC7517 JSON Web Key som innehåller certifikatet som ska läggas till. JWT måste signeras med privat nyckel för något av de giltiga princip signerarcertifikat som är associerade med providern. Se [exempel på principtecken.](./policy-signer-examples.md)

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Konfigurationsfel för att bestationeringsprincip

**Felkod** PolicyParsingError

**Scenarioexempel** Princip med felaktig syntax (till exempel om det saknas semikolon)/giltig JWT-princip)

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

**Scenarioexempel** Ogiltigt innehåll tillhandahålls (till exempel ladda upp princip/osignerad princip när principsignering krävs)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Felsökningssteg** Kontrollera att principen i textformat är UTF-8-kodad.

Om principsignering krävs måste attestationsprincipen endast konfigureras i FORMATET RFC7519 JSON Web Token (JWT). Om principsignering inte krävs kan principen konfigureras i text- eller JWT-format.

Om du vill konfigurera en princip i JWT-format använder du JWT med ett anspråk med namnet "AttestationPolicy". Värdet för anspråket är Base64URL-kodad version av principtexten. Om attesteringsprovidern har konfigurerats med certifikat för principtecken måste JWT signeras med en privat nyckel för något av de giltiga princip signerarcertifikat som är associerade med providern. 

Om du vill konfigurera en princip i textformat anger du principtext direkt.

I PowerShell anger du PolicyFormat som JWT för att konfigurera principen i JWT-format. Standardprincipformatet är Text.

Se exempel [](./policy-examples.md) på attestationprincip och [hur du skapar en attestationsprincip](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Installationsproblem med Az.Attestation i PowerShell

Det går inte att installera Az- eller Az.Attestation-moduler i PowerShell

### <a name="error"></a>Fel

VARNING! Det gick inte att matcha paketkällan https://www.powershellgallery.com/api/v2 PackageManagement\Install-Package: Ingen matchning hittades för de angivna sökvillkoren och modulnamnet

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

PowerShell-galleriet har inaktuella versioner Transport Layer Security (TLS) 1.0 och 1.1. 

TLS 1.2 eller senare rekommenderas. 

Om du vill fortsätta att interagera med PowerShell-galleriet kör du följande kommando före Install-Module kommandona

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Problem med principåtkomst/konfiguration i PowerShell

Användare som tilldelats med lämpliga roller. Men du får auktoriseringsproblem när du hanterar attestationsprinciper via PowerShell.

### <a name="error"></a>Fel
Klienten med objekt-ID för objekt-ID har inte behörighet att utföra åtgärden &lt; &gt;  Microsoft.Authorization/roleassignments/write över omfånget 'subcriptions/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/ role assignmentId ' eller så är omfånget &lt; &gt; ogiltigt. Uppdatera dina autentiseringsuppgifter om åtkomsten nyligen har beviljats

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Den lägsta versionen av Az-moduler som krävs för att stödja attestationsåtgärder är följande: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Kör kommandot nedan för att verifiera den installerade versionen av alla Az-moduler 

```powershell
Get-InstalledModule 
```

Om versionerna inte matchar minimikravet kör du Update-Module kommandon

t.ex. - Update-Module -Name Az.Attestation
