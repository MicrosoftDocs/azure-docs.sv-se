---
title: Konfigurera Azure Attestation med Azure PowerShell
description: Så här konfigurerar och konfigurerar du en attestationsprovider med hjälp Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a343c1729b47d88ec414f17fcef7f9323d99161d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515268"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Snabbstart: Konfigurera Azure Attestation med Azure PowerShell

Följ stegen nedan för att skapa och konfigurera en attestationsprovider med hjälp av Azure PowerShell. Se [Översikt över Azure PowerShell](/powershell/azure/) information om hur du installerar och kör Azure PowerShell.

Observera att versionen PowerShell-galleriet inaktuella versioner Transport Layer Security (TLS) 1.0 och 1.1. TLS 1.2 eller senare rekommenderas. Därför kan du få följande fel:

- VARNING: Det går inte att matcha paketkällan https://www.powershellgallery.com/api/v2 ' '
- PackageManagement\Install-Package: Ingen matchning hittades för de angivna sökvillkoren och modulnamnet 

Om du vill fortsätta att interagera med PowerShell-galleriet kör du följande kommando före Install-Module kommandona

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Installera Az.Attestation PowerShell-modulen

På en dator Azure PowerShell installerar du PowerShell-modulen Az.Attestation, som innehåller cmdlets för Azure Attestation.  

### <a name="initial-installation"></a>Inledande installation

Avsluta alla befintliga PowerShell-fönster.

Om du vill installera för "aktuell användare" startar du ett PowerShell-fönster utan förhöjd behörighet och kör:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Om du vill installera för "alla användare" startar du ett upphöjt PowerShell-fönster och kör:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Stäng den upphöjda PowerShell-konsolen.

### <a name="update-the-installation"></a>Uppdatera installationen

Avsluta alla befintliga PowerShell-fönster.

Om du vill uppdatera för "aktuell användare" startar du ett PowerShell-fönster utan förhöjd behörighet och kör:

```powershell
Update-Module -Name Az.Attestation
```

Om du vill uppdatera för "alla användare" startar du ett upphöjt PowerShell-fönster och kör:

```powershell
Update-Module -Name Az.Attestation
```

Stäng den upphöjda PowerShell-konsolen.

### <a name="get-installed-modules"></a>Hämta installerade moduler

Lägsta version av Az-moduler som krävs för att stödja attestationsåtgärder:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Kör kommandot nedan för att verifiera den installerade versionen av alla Az-moduler 

```powershell
Get-InstalledModule
```
Om versionerna inte matchar minimikravet kör du Update-Module kommandon.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure i PowerShell-konsolen (utan utökade åtkomstbehörigheter).

```powershell
Connect-AzAccount
```

Om det behövs växlar du till prenumerationen som ska användas för Azure Attestation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registrera resursprovidern Microsoft.Attestation

Registrera resursprovidern Microsoft.Attestation i prenumerationen. Mer information om Azure-resursproviders och hur du konfigurerar och hanterar resursproviders finns i [Resursproviders och resurstyper i Azure.](../azure-resource-manager/management/resource-providers-and-types.md) Observera att registrering av en resursprovider bara krävs en gång för en prenumeration.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regional tillgänglighet för Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resursgrupp

Skapa en resursgrupp för attestationsprovidern. Observera att andra Azure-resurser (inklusive en virtuell dator med klientprograminstans) kan läggas i samma resursgrupp.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

 > [!NOTE]
   > När en attesteringsprovider har skapats i den här resursgruppen måste en Azure AD-användare ha rollen "Attesteringsdeltagare" på providern för att utföra åtgärder som principkonfiguration/hantering av princip signerarcertifikat. Dessa behörigheter kan också ärvas med roller som "Ägare" (behörigheter med jokertecken), "Deltagare" (behörigheter med jokertecken) i prenumerationen/resursgruppen.  


## <a name="create-and-manage-an-attestation-provider"></a>Skapa och hantera en attestationsprovider

New-AzAttestation skapar en attestationsprovider.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile är en fil som anger en uppsättning betrodda signeringsnycklar. Om ett filnamn har angetts för parametern PolicySignerCertificateFile kan attstationsprovidern endast konfigureras med principer i signerat JWT-format. Else-principen kan konfigureras i text eller ett osignerat JWT-format.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Exempel på PolicySignersCertificateFile finns i [exempel på certifikat för principsignerare.](policy-signer-examples.md)

Get-AzAttestation hämtar egenskaperna för attesteringsprovidern som status och AttestURI. Anteckna AttestURI eftersom det behövs senare.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Kommandot ovan bör generera utdata som liknar följande: 

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

Attestationsproviders kan tas bort med hjälp Remove-AzAttestation cmdlet.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Principhantering

För att kunna hantera principer kräver en Azure AD-användare följande behörigheter för "Åtgärder":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

 För att utföra de här åtgärderna måste en Azure AD-användare ha rollen "Attestation Contributor" (Attestation-deltagare) på attestationsprovidern. Dessa behörigheter kan också ärvas med roller som "Ägare" (behörigheter med jokertecken), "Deltagare" (behörigheter med jokertecken) i prenumerationen/resursgruppen.  

För att kunna läsa principer kräver en Azure AD-användare följande behörighet för "Åtgärder":
- Microsoft.Attestation/attestationProviders/attestation/read

 För att utföra den här åtgärden måste en Azure AD-användare ha rollen "Attestation Reader" på attestationsprovidern. Läsbehörigheten kan också ärvas med roller som "läsare" (behörigheter med jokertecken) i prenumerationen/resursgruppen.  

Nedanstående PowerShell-cmdlets tillhandahåller principhantering för en attestationsprovider (enERING i taget).

Get-AzAttestationPolicy returnerar den aktuella principen för den angivna FUNKTIONEN. Cmdleten visar principen i både text- och JWT-format för principen.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

DE TYPER som stöds är "SgxEnclave", "OpenEnclave" och "VbsEnclave".

Set-AttestationPolicy anger en ny princip för den angivna PRINCIPEN. Cmdleten accepterar principen i antingen text- eller JWT-format och styrs av parametern PolicyFormat. "Text" är standardvärdet för PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Om PolicySignerCertificateFile anges när en attestationsprovider skapas kan principer endast konfigureras i signerat JWT-format. Else-principen kan konfigureras i text eller ett osignerat JWT-format.

Attestationsprincipen i JWT-format måste innehålla ett anspråk med namnet "AttestationPolicy". För signerade policyer måste JWT vara signerat med en privat nyckel som motsvarar något av de befintliga princip signerarcertifikaten.

Exempel på principexempel finns [i exempel på en attestationsprincip.](policy-examples.md)

Reset-AzAttestationPolicy återställer principen till standard för angivenPOR.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Certifikathantering för princip signerare

Nedanstående PowerShell-cmdlets ger hantering av princip signerarcertifikat för en attesteringsprovider:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Certifikat för principtecken är ett signerat JWT med anspråket "maa-policyCertificate". Anspråksvärdet är en JWK som innehåller den betrodda signeringsnyckeln som ska läggas till. JWT måste signeras med en privat nyckel som motsvarar något av de befintliga princip signerarcertifikaten.

Observera att all semantisk manipulering av princip signerarcertifikatet måste göras utanför PowerShell. När det gäller PowerShell är det en enkel sträng.

Exempel på certifikat för princip signerare finns [i exempel på certifikat för princip signerare](policy-signer-examples.md).

Mer information om cmdletarna och dess parametrar finns i [Azure Attestation PowerShell-cmdlets](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en attestationsprincip](author-sign-policy.md)
- [Attestera en SGX-enklav med hjälp av kodexempel](/samples/browse/?expanded=azure&terms=attestation)
