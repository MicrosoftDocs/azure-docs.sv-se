---
title: Konfigurera Azure-attestering med Azure PowerShell
description: Konfigurera och konfigurera en attesterings leverantör med Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc415411e05d6fdecee1acf2fbc02b3c170b9d6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501132"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Snabb start: Konfigurera Azure-attestering med Azure PowerShell

Följ stegen nedan för att skapa och konfigurera en attesterings-Provider med hjälp av Azure PowerShell. Se [Översikt över Azure PowerShell](/powershell/azure/) för information om hur du installerar och kör Azure PowerShell.

Observera att PowerShell-galleriet har föråldrade Transport Layer Security (TLS) version 1,0 och 1,1. TLS 1,2 eller en senare version rekommenderas. Därför kan följande fel uppstå:

- Varning: det går inte att matcha paket källan https://www.powershellgallery.com/api/v2
- PackageManagement\Install-Package: ingen matchning hittades för de angivna Sök kriterierna och modulnamnet 

Om du vill fortsätta att interagera med PowerShell-galleriet kör du följande kommando innan du Install-Module kommandon

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Installera AZ. attestering PowerShell-modul

På datorn med Azure PowerShell installerar du PowerShell-modulen AZ. attestering, som innehåller cmdletar för Azure-attestering.  

### <a name="initial-installation"></a>Första installationen

Avsluta alla befintliga PowerShell-fönster.

Om du vill installera för "Current User" startar du ett icke upphöjt PowerShell-fönster och kör:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Om du vill installera för "alla användare" startar du ett upphöjd PowerShell-fönster och kör:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Stäng den upphöjda PowerShell-konsolen.

### <a name="update-the-installation"></a>Uppdatera installationen

Avsluta alla befintliga PowerShell-fönster.

Om du vill uppdatera för "aktuell användare" startar du ett icke upphöjt PowerShell-fönster och kör:

```powershell
Update-Module -Name Az.Attestation
```

Om du vill uppdatera för "alla användare" startar du ett upphöjd PowerShell-fönster och kör:

```powershell
Update-Module -Name Az.Attestation
```

Stäng den upphöjda PowerShell-konsolen.

### <a name="get-installed-modules"></a>Hämta installerade moduler

Lägsta version av AZ-moduler som krävs för att stödja attesterings åtgärder:
- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. attestering 0.1.8

Kör kommandot nedan för att kontrol lera den installerade versionen av alla AZ-moduler 

```powershell
Get-InstalledModule
```
Om versionerna inte matchar minimi kravet kör Update-Module-kommandon.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure i PowerShell-konsolen (utan utökade åtkomst privilegier).

```powershell
Connect-AzAccount
```

Om det behövs växlar du till den prenumeration som ska användas för Azure-attestering.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registrera Microsoft. attestering Resource Provider

Registrera Microsoft. attesterings resurs leverantören i prenumerationen. Mer information om Azure-resurstjänster och hur du konfigurerar och hanterar resurs leverantörer finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md). Observera att registrering av en resurs leverantör krävs bara en gång för en prenumeration.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regional tillgänglighet för Azure-attestering

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resurs grupp

Skapa en resurs grupp för attesterings leverantören. Observera att andra Azure-resurser (inklusive en virtuell dator med klient program instansen) kan placeras i samma resurs grupp.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Skapa och hantera en attesterings leverantör

New-AzAttestation skapar en attesterings leverantör.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile är en fil som anger en uppsättning betrodda signerings nycklar. Om ett fil namn har angetts för parametern PolicySignerCertificateFile kan attesterings leverantören bara konfigureras med principer i signerat JWT-format. Andra principer kan konfigureras i text eller osignerat JWT-format.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

PolicySignersCertificateFile-exempel finns i [exempel på princip signerings certifikat](policy-signer-examples.md).

Get-AzAttestation hämtar egenskaperna för attesteringsservern som status och AttestURI. Anteckna AttestURI, eftersom det kommer att behövas senare.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Kommandot ovan ska skapa utdata som liknar dem nedan: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Attesterings leverantörer kan tas bort med hjälp av Remove-AzAttestation-cmdleten.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Principhantering

För att kunna hantera principer kräver en Azure AD-användare följande behörigheter för "åtgärder":
- Microsoft. attestering/attestationProviders/attestering/läsning
- Microsoft. attestering/attestationProviders/attestering/skrivning
- Microsoft. attestering/attestationProviders/attestering/Delete

Dessa behörigheter kan tilldelas till en AD-användare via en roll som "ägare" (behörigheter för jokertecken), "deltagare" (behörigheter för jokertecken) eller "attesterings bidrags givare" (endast vissa behörigheter för Azure-attestering).  

För att kunna läsa principer kräver en Azure AD-användare följande behörighet för "åtgärder":
- Microsoft. attestering/attestationProviders/attestering/läsning

Den här behörigheten kan tilldelas till en AD-användare via en roll som "läsare" (behörighet som jokertecken) eller "attesterings läsare" (endast vissa behörigheter för Azure-attestering).

Under PowerShell-cmdletar tillhandahåller princip hantering för en attesterings leverantör (en TEE i taget).

Get-AzAttestationPolicy returnerar den aktuella principen för den angivna TEE. Cmdleten visar principen i både text-och JWT-format för principen.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

TEE-typer som stöds är "SgxEnclave", "OpenEnclave" och "VbsEnclave".

Set-AttestationPolicy anger en ny princip för angivet TEE. Cmdlet: en accepterar en princip i text-eller JWT-format och styrs av PolicyFormat-parametern. "Text" är standardvärdet för PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Om PolicySignerCertificateFile anges när du skapar en attesterings leverantör kan principer endast konfigureras i signerat JWT-format. Andra principer kan konfigureras i text eller osignerat JWT-format.

Attesterings principen i JWT-format måste innehålla ett anspråk med namnet "AttestationPolicy". För signerade principer måste JWT vara signerat med en privat nyckel som motsvarar något av de befintliga inloggnings certifikaten för principen.

Princip exempel finns i [exempel på en attesterings princip](policy-examples.md).

Reset-AzAttestationPolicy återställer principen till standardvärdet för den angivna TEE.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Hantering av princip inloggnings certifikat

Under PowerShell-cmdlets får du hantering av princip inloggnings certifikat för en attesterings leverantör:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Princip signerings certifikat är ett signerat JWT med anspråk med namnet "Maa-policyCertificate". Värdet för anspråket är en JWK som innehåller den betrodda signerings nyckeln som ska läggas till. JWT måste vara signerat med en privat nyckel som motsvarar något av de befintliga inloggnings certifikaten för principen.

Observera att all semantisk manipulering av princip signerings certifikatet måste göras utanför PowerShell. Så långt som det gäller PowerShell är det en enkel sträng.

Exempel på princip signerings certifikat finns i [exempel på princip signerings certifikat](policy-signer-examples.md).

Mer information om cmdletarna och dess parametrar finns i PowerShell- [cmdletar för Azure-attestering](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Intyga en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)
